---
title: "R Shiny Sub Module"
description: |
  Assignment
author:
  - name: Joey Chua
    url: https://www.linkedin.com/in/joeychuasm/
date: 04-10-2021
output:
  distill::distill_article:
    toc: true
    toc_depth: 6
    self_contained: false
#runtime: shiny

---



# Introduction 

Airbnb is an online vacation rental marketplace servicing a community of hosts and travellers. The diagram below shows the process of how Airbnb started with two individuals who could not pay for rent in 2007 to starting a company that reached US$10 billion valuation by 2014.In 2020, Airbnb went public with valuation of up to US$47 million. [valuation of up to US$47 million](https://blog.seedly.sg/airbnb-ipo/)

![](airbnbhistory.png)
[Adapted from Adioma](https://blog.adioma.com/how-airbnb-started-infographic/)


According to [Airbnb](https://news.airbnb.com/2020-update/), Airbnb has millions of listings in over 220 counties and regions across 100,000 cities. The data generated provides rich information, including structured data e.g. price and location, as well as unstructured data e.g. reviews and listing descriptions. While there are statistical and analytic tools available to derive insights using these data, these tools are often subscription-based and require technical knowledge, which may not be available or accessible to everyone. Hence, this project aims to develop an interface which is concise, interactive, and user-friendly using R Shiny. With this interface, data-based decisions can be made from the interactive GUI. The R Shiny app will cover exploratory data analysis, confirmatory data analysis, text mining, as well as predictive analysis. 

This assignment is sub-module of our final Shiny-based Visual Analytics Application (Shiny-VAA). In particular, a focus on text mining utilising various R packages will be presented. The process is shown below: 

## Application Use Case 

Our application can be used from both the perspective of hosts and guests.

**Hosts**: In 2014, Airbnb launched the Superhost programme to reward hosts with outstanding hospitality. As a Superhost, one will have better earnings, more visibility, and are able to earn exclusive rewards such as increased earnings compared to regular hosts. To become a Superhost, these are the criteria to be met:
- 4.8 or higher overall rating based on reviews
- Completed at least 10 stays in the past year or 100 nights over at least 3 completed stays
- Less than 1% cancellation rate, not including extenuating circumstances
- Responds to 90% of new messages within 24 hours 

**Guests**: With over 60,000 members and 6,000 properties listed on Airbnb website, a dilemma on which is the right space might be of concern to users. Various modules in our dashboard will allow both types of users to analyse Airbnb data according to their needs. 

## Data 

[InsideAirbnb](http://insideairbnb.com/get-the-data.html) provides tools and data for users to explore Airbnb. We will be using the following files:
- listing.csv.gz: This dataset consists of 74 variables and 4256 data points.  
- reviews.csv.gz: This dataset provides 6 variables and 52368 data points.  
While the team has decided to use the latest set of data compiled on 27 January 2021, this report uses data compiled on 29 December 2020 for completeness. 

# Literature Review

_Conducting literature review on how the analysis were performed before. The focus should be on identifying gaps whereby interactive web approach and visual analytics techniques can be used to enhance user experience on using the analysis techniques._


Airbnb data has been widely used for text mining in tools like Python and R. In Python, (Natural Language Processing Toolkit)[https://www.nltk.org/] has easy-to-use interfaces to over 50 corpora and lexical resource, as well as a wide range of text processing libraries for tokenisation, stemming, classification etc. Similarly, R has extensive libraries such as tidyverse and Shiny which allows for text mining and building of interactive dashboards. 

Zhang (2019) used text mining approaches including content analysis and topic modelling (Latent Dirichlet Allocation (LDA) method) to examine over 1 million Airbnb reviews across 50,933 listings in the United States of America (USA). Kiatkawsin, Sutherland & Kim (2020) also used LDA method to compare reviews between Hong Kong and Singapore. However, these articles do not provide visualiation of the methods used and are not interactive. 

[Kim's Shiny Airbnb App](https://donghwikim21.shinyapps.io/ShinyAirbnb/) provided dashboard which is interactive for Exploratory Data Analysis (EDA), but left out reviews. [Ankit Pandey] (https://github.com/ankit2web/Twitter-Sentiment-Analysis-using-R-Shiny-WebApp) provided a more comprehensive text analytics dashboard using wordcloud and polarity of sentiments, but does not provide much interactivity. 

To solve the above gaps, the next section outlines the steps:

# Submodules 

## Data Preparation

_Extracting, wrangling and preparing the input data required to perform the analysis. The focus should be on exploring appropriate tidyverse methods_

### R Markdown

runtime:shiny was added to allow dynamic documentation.
{r} part of the code chunk can be used to specify elements and subsequently rendered into different format.
echo=TRUE is set to allow printing of code chunk when rendered into a different file format.
More details can be found at [R Markdown Documentation](https://rmarkdown.rstudio.com/lesson-1.html).

### Packages

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>packages</span> <span class='op'>&lt;-</span> <span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='st'>"tidyverse"</span>,<span class='st'>"sf"</span>,<span class='st'>"tmap"</span>,<span class='st'>"crosstalk"</span>,<span class='st'>"leaflet"</span>,<span class='st'>"RColorBrewer"</span>,<span class='st'>"ggplot2"</span>,<span class='st'>"rgdal"</span>, <span class='st'>"rgeos"</span>, <span class='st'>"raster"</span>, <span class='st'>"maptools"</span>,<span class='st'>"tmaptools"</span>,<span class='st'>"shiny"</span>,<span class='st'>"tidytext"</span>,<span class='st'>"wordcloud"</span>,<span class='st'>"wordcloud2"</span>,<span class='st'>"tm"</span>,<span class='st'>"ggthemes"</span>,<span class='st'>"igraph"</span>,<span class='st'>"ggmap"</span>,<span class='st'>"DT"</span>,<span class='st'>"reshape2"</span>,<span class='st'>"ggraph"</span>,<span class='st'>"topicmodels"</span>,<span class='st'>"tidytext"</span>,<span class='st'>"topicmodels"</span>,<span class='st'>"quanteda"</span>,<span class='st'>"tm"</span>,<span class='st'>"RColorBrewer"</span>,<span class='st'>"DataExplorer"</span><span class='op'>)</span>

<span class='kw'>for</span> <span class='op'>(</span><span class='va'>p</span> <span class='kw'>in</span> <span class='va'>packages</span><span class='op'>)</span><span class='op'>{</span>
  <span class='kw'>if</span> <span class='op'>(</span><span class='op'>!</span><span class='kw'><a href='https://rdrr.io/r/base/library.html'>require</a></span><span class='op'>(</span><span class='va'>p</span>,character.only<span class='op'>=</span><span class='cn'>T</span><span class='op'>)</span><span class='op'>)</span><span class='op'>{</span>
    <span class='fu'><a href='https://rdrr.io/r/utils/install.packages.html'>install.packages</a></span><span class='op'>(</span><span class='va'>p</span><span class='op'>)</span>
  <span class='op'>}</span>
  <span class='kw'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='op'>(</span><span class='va'>p</span>, character.only<span class='op'>=</span><span class='cn'>T</span><span class='op'>)</span>
<span class='op'>}</span>
</code></pre></div>

</div>


### Import Data

Read file and retain necessary columns
- review file contains 52367 observations with 6 variables;only 2 columns (listing_id and comments) are retained.
- listings file contains 4255 observations with 74 variables; 33 columns are retained.

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>reviews</span> <span class='op'>&lt;-</span> <span class='fu'>read_csv</span><span class='op'>(</span><span class='st'>"C:/Users/joeyc/blog/_posts/2021-03-29-assignment/reviews.csv"</span><span class='op'>)</span><span class='op'>%&gt;%</span> 
  <span class='fu'>dplyr</span><span class='fu'>::</span><span class='fu'><a href='https://dplyr.tidyverse.org/reference/select.html'>select</a></span><span class='op'>(</span><span class='va'>listing_id</span>,<span class='va'>comments</span><span class='op'>)</span>
</code></pre></div>

</div>


<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>listings</span> <span class='op'>&lt;-</span> <span class='fu'>read_csv</span><span class='op'>(</span><span class='st'>"C:/Users/joeyc/blog/_posts/2021-03-29-assignment/listings.csv"</span><span class='op'>)</span>  <span class='op'>%&gt;%</span> 
  <span class='fu'>rename</span><span class='op'>(</span>listing_id<span class='op'>=</span><span class='va'>id</span><span class='op'>)</span> <span class='op'>%&gt;%</span> 
  <span class='fu'>dplyr</span><span class='fu'>::</span><span class='fu'><a href='https://dplyr.tidyverse.org/reference/select.html'>select</a></span><span class='op'>(</span><span class='op'>-</span><span class='fu'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='op'>(</span><span class='va'>listing_url</span>, <span class='va'>scrape_id</span>, <span class='va'>last_scraped</span>, <span class='va'>name</span>, <span class='va'>picture_url</span>,<span class='va'>host_url</span>, <span class='va'>host_about</span>,<span class='va'>host_thumbnail_url</span>, <span class='va'>host_picture_url</span>, <span class='va'>host_listings_count</span>, <span class='va'>host_verifications</span>,<span class='va'>calendar_updated</span>,<span class='va'>first_review</span>,<span class='va'>last_review</span>,<span class='va'>license</span>,<span class='va'>neighborhood_overview</span>,<span class='va'>description</span>,<span class='va'>host_total_listings_count</span>,<span class='va'>host_has_profile_pic</span>,<span class='va'>availability_30</span>,<span class='va'>availability_60</span>,<span class='va'>availability_90</span>,<span class='va'>availability_365</span>,<span class='va'>calculated_host_listings_count</span>,<span class='va'>calculated_host_listings_count_entire_homes</span>,<span class='va'>calculated_host_listings_count_private_rooms</span>,<span class='va'>calculated_host_listings_count_shared_rooms</span>,<span class='va'>reviews_per_month</span>,<span class='va'>minimum_nights</span>,<span class='va'>maximum_nights</span>,<span class='va'>minimum_minimum_nights</span>,<span class='va'>maximum_minimum_nights</span>,<span class='va'>minimum_maximum_nights</span>,<span class='va'>maximum_maximum_nights</span>,<span class='va'>number_of_reviews_ltm</span>,<span class='va'>number_of_reviews_l30d</span>,<span class='va'>minimum_nights_avg_ntm</span>,<span class='va'>maximum_nights_avg_ntm</span>,<span class='va'>calendar_last_scraped</span>,<span class='va'>has_availability</span>,<span class='va'>instant_bookable</span><span class='op'>)</span><span class='op'>)</span>
</code></pre></div>

</div>


### Merge Data

Merge the listing and review files

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='va'>data</span> <span class='op'>&lt;-</span> <span class='fu'>right_join</span><span class='op'>(</span><span class='va'>reviews</span>,<span class='va'>listings</span>,by<span class='op'>=</span><span class='st'>"listing_id"</span><span class='op'>)</span>
</code></pre></div>

</div>


### Save file

Write to CSV for future usage

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='co'>#write.csv(data,"data.csv")</span>
</code></pre></div>

</div>


### View data

<div class="layout-chunk" data-layout="l-body">
<div class="sourceCode"><pre class="sourceCode r"><code class="sourceCode r"><span class='fu'>glimpse</span><span class='op'>(</span><span class='va'>data</span><span class='op'>)</span>
</code></pre></div>

```
Rows: 54,074
Columns: 34
$ listing_id                   <dbl> 49091, 50646, 50646, 50646, 506~
$ comments                     <chr> "Fran was absolutely gracious a~
$ host_id                      <dbl> 266763, 227796, 227796, 227796,~
$ host_name                    <chr> "Francesca", "Sujatha", "Sujath~
$ host_since                   <date> 2010-10-20, 2010-09-08, 2010-0~
$ host_location                <chr> "Singapore", "Singapore, Singap~
$ host_response_time           <chr> "within a few hours", "a few da~
$ host_response_rate           <chr> "100%", "0%", "0%", "0%", "0%",~
$ host_acceptance_rate         <chr> "N/A", "N/A", "N/A", "N/A", "N/~
$ host_is_superhost            <lgl> FALSE, FALSE, FALSE, FALSE, FAL~
$ host_neighbourhood           <chr> "Woodlands", "Bukit Timah", "Bu~
$ host_identity_verified       <lgl> TRUE, TRUE, TRUE, TRUE, TRUE, T~
$ neighbourhood                <chr> NA, "Singapore, Singapore", "Si~
$ neighbourhood_cleansed       <chr> "Woodlands", "Bukit Timah", "Bu~
$ neighbourhood_group_cleansed <chr> "North Region", "Central Region~
$ latitude                     <dbl> 1.44255, 1.33235, 1.33235, 1.33~
$ longitude                    <dbl> 103.7958, 103.7852, 103.7852, 1~
$ property_type                <chr> "Private room in apartment", "P~
$ room_type                    <chr> "Private room", "Private room",~
$ accommodates                 <dbl> 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2~
$ bathrooms                    <lgl> NA, NA, NA, NA, NA, NA, NA, NA,~
$ bathrooms_text               <chr> "1 bath", "1 bath", "1 bath", "~
$ bedrooms                     <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1~
$ beds                         <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1~
$ amenities                    <chr> "[\"Washer\", \"Elevator\", \"L~
$ price                        <chr> "$80.00", "$80.00", "$80.00", "~
$ number_of_reviews            <dbl> 1, 18, 18, 18, 18, 18, 18, 18, ~
$ review_scores_rating         <dbl> 94, 91, 91, 91, 91, 91, 91, 91,~
$ review_scores_accuracy       <dbl> 10, 9, 9, 9, 9, 9, 9, 9, 9, 9, ~
$ review_scores_cleanliness    <dbl> 10, 10, 10, 10, 10, 10, 10, 10,~
$ review_scores_checkin        <dbl> 10, 10, 10, 10, 10, 10, 10, 10,~
$ review_scores_communication  <dbl> 10, 10, 10, 10, 10, 10, 10, 10,~
$ review_scores_location       <dbl> 8, 9, 9, 9, 9, 9, 9, 9, 9, 9, 9~
$ review_scores_value          <dbl> 8, 9, 9, 9, 9, 9, 9, 9, 9, 9, 9~
```

</div>


glimpse(reviews) does not present the data in a tabular format, datatable and kable packages were considered.
- datatable does not work well with the extensions of FixedColumns, FixedHeader and Scoller when coupled with Shiny. Hence, these specific functionalities are excluded.
- kable is not up to date with the current version of R and was not used.

