[![Launch Rstudio
Binder](http://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/raynamharris/Venn-v-UpSet/master?urlpath=rstudio)

Please note that the full tutorial with context can be found on [this
blog](https://www.littlemissdata.com/blog/set-analysis)

Install and Load Packages
=========================

Before we get going, we will install and load all required packages.
Please uncomment any lines that contain packages which you don’t already
have.

Important notes about venneuler java use. - As far as I can tell, [the
venneuler
package](https://cran.r-project.org/web/packages/UpSetR/vignettes/basic.usage.html)
does not work with Java v12. - If you have [v11
JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)
installed and you still have issues, please see this [troubleshooting
issue.](https://github.com/s-u/rJava/issues/151).

    # see https://github.com/rstudio/rstudio/issues/2254 for rJava install

    library(rJava)
    library(UpSetR)
    library(tidyverse)

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.0.9000     ✓ purrr   0.3.3     
    ## ✓ tibble  2.1.3          ✓ dplyr   0.8.3     
    ## ✓ tidyr   1.0.0          ✓ stringr 1.4.0     
    ## ✓ readr   1.3.1          ✓ forcats 0.4.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    library(venneuler)
    library(grid)

Round 1: Tiny and Fun Set Intersection
======================================

Venn diagrams are fun, so let’s start with that!

Assign the Set Sizing
---------------------

    # Data
    expressionInput <- c(`#rstats` = 5, memes = 5, `#rstats&memes` = 3)

Create a Venn Diagram
---------------------

    # Venn
    # note on set up for java v11 jdk (v12 does not work with this)
    myExpVenn <- venneuler(expressionInput)
    par(cex=1.2)
    plot(myExpVenn, main = "Why I Love Twitter")
    grid.text(
      "@littlemissdata",
      x = 0.52,
      y = 0.2,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-2-1.png)

Create an UpsetR Chart
----------------------

To orient yourself to the chart, note that the overall count is in the
horizontal bar on the left hand side. The individual set counts
represent the bar. For example, the \#rstats set has 8 total, 3
overlapping with the meme set and 5 belonging just to the \#rstats set.

I think UpsetR is a great way to easily parse through the relative sizes
of the sets.

    # UpsetR
    upset(fromExpression(expressionInput), order.by = "freq")
    grid.text(
      "Why I Love Twitter  @littlemissdata",
      x = 0.80,
      y = 0.05,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    ) # created the label via grid.text with hack found here: https://github.com/hms-dbmi/UpSetR/issues/76

![](README_files/figure-markdown_strict/unnamed-chunk-3-1.png)

Round 2: Complicated Sets
=========================

Bring in Data
-------------

The data set we are going to work with is about 7K records and it is
based on the [2017 Toronto Senior
Survey](https://www.toronto.ca/city-government/data-research-maps/open-data/open-data-catalogue/community-services/#9ece3c85-08c9-097d-f4c8-bb7374fea6c1)
from the [Toronto Open Data Catalogue](Open%20Data%20Catalogue). Having
lived in Toronto for 10 years, I’m so happy that they have created an
open data portal. You have to give the data to the people that pay for
it! I also love that Toronto is invested in the senior community and
actively monitoring their quality of life.

I have taken the original data set and stripped it down to include
general questions relating to the person as well as transportation
questions. I removed the mostly blank rows and renamed the columns.
Below is the mapping and data dictionary.

<table style="width:39%;">
<colgroup>
<col width="19%" />
<col width="19%" />
</colgroup>
<thead>
<tr class="header">
<th>
Column
</th>
<th>
Source Column
</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>
ID
</td>
<td>
Not previously included. This is a new unique key column.
</td>
</tr>
<tr class="even">
<td>
physicalActivity
</td>
<td>
Survey Question: “1. In the past 3 months, how often did you participate
in physical activities like walking?”
</td>
</tr>
<tr class="odd">
<td>
physicalActivityPerMonth
</td>
<td>
Survey Question: “1. In the past 3 months, how often did you participate
in physical activities like walking?”. This has been transformed into
numerical format.
</td>
</tr>
<tr class="even">
<td>
volunteerParticipation
</td>
<td>
Survey Question: “5. During the past 3 months, how often did you
participate in volunteer or charity work?”
</td>
</tr>
<tr class="odd">
<td>
volunteerPerMonth
</td>
<td>
Survey Question: “5. During the past 3 months, how often did you
participate in volunteer or charity work?”. This has been transformed
into numerical format.
</td>
</tr>
<tr class="even">
<td>
difficultFinancial
</td>
<td>
Survey Question: “9. In the last year, have you had difficulty paying
your rent, mortgage, Hydro bill, or other housing costs? For example,
have you had to go without groceries to pay for rent or other monthly
housing expenses?”
</td>
</tr>
<tr class="odd">
<td>
supportSystem
</td>
<td>
Survey Question: “13. Do you have people in your life who you can call
on for help if you need it?”
</td>
</tr>
<tr class="even">
<td>
postalCode
</td>
<td>
“Survey Question: 14. What are the first three characters of your postal
code?”
</td>
</tr>
<tr class="odd">
<td>
employmentStatus
</td>
<td>
Survey Question: “15. What is your current employment status?”
</td>
</tr>
<tr class="even">
<td>
sex
</td>
<td>
Survey Question: “16. What is your sex/gender?”
</td>
</tr>
<tr class="odd">
<td>
primaryLanguage
</td>
<td>
Survey Question: “18. In what language(s) would you feel most
comfortable to receive services?” (first option listed)
</td>
</tr>
<tr class="even">
<td>
ageRange
</td>
<td>
Survey Question: “19. Which age category do you belong to?”
</td>
</tr>
<tr class="odd">
<td>
ttcTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[TTC (bus, subway, or streetcar)\]”
</td>
</tr>
<tr class="even">
<td>
walkTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[Walk\]”
</td>
</tr>
<tr class="odd">
<td>
driveTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[Drive\]”
</td>
</tr>
<tr class="even">
<td>
cycleTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[Cycle\]”
</td>
</tr>
<tr class="odd">
<td>
taxiTransportation
</td>
<td>
Survey Question: " 6. To get around Toronto, what modes of
transportation do you use frequently? \[Taxi or Uber\]"
</td>
</tr>
<tr class="even">
<td>
communityRideTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[Community Transportation Program, for example
Toronto Ride or iRIDE\]”
</td>
</tr>
<tr class="odd">
<td>
wheelTransTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[Wheel-Trans\]”
</td>
</tr>
<tr class="even">
<td>
friendsTransportation
</td>
<td>
Survey Question: “6. To get around Toronto, what modes of transportation
do you use frequently? \[Rides from family, friends or neighbours\]”
</td>
</tr>
<tr class="odd">
<td>
ageRange
</td>
<td>
Survey Question: “19. Which age category do you belong to?”.
</td>
</tr>
<tr class="even">
<td>
minAgeRange
</td>
<td>
Survey Question: “19. Which age category do you belong to?”. This has
been converted to numerical format, taking the lowest age as the value.
</td>
</tr>
</tbody>
</table>

    rawSets <- read.csv(
      file = "https://raw.githubusercontent.com/lgellis/MiscTutorial/master/sets/seniorTransportation.csv",
      header = TRUE, sep = ",", stringsAsFactors = FALSE
    )

    # Some formatting

    # Replace the NA's
    rawSets[is.na(rawSets)] <- 0


    # Rename the columns for easier display
    sets <- rawSets %>%
      rename(TTC = ttcTransportation, Walk = walkTransportation, Drive = driveTransportation, Cycle = cycleTransportation, Taxi = taxiTransportation, `Community Ride` = communityRideTransportation, `Wheel Trans` = wheelTransTransportation, Friends = friendsTransportation)

    dim(sets)

    ## [1] 6239   21

    head(sets)

    ##   ID physicalActivity physicalActivityPerMonth volunteerParticipation
    ## 1  1            Never                        0                       
    ## 2  2            Never                        0                  Never
    ## 3  3            Never                        0                  Never
    ## 4  4            Never                        0                  Never
    ## 5  5            Never                        0  At least once a month
    ## 6  6            Never                        0  At least once a month
    ##   volunteerPerMonth TTC Walk Drive Cycle Taxi Community Ride Wheel Trans
    ## 1                 0   0    0     1     0    0              0           0
    ## 2                 0   0    0     1     0    0              0           0
    ## 3                 0   0    0     1     0    0              0           0
    ## 4                 0   0    0     1     0    0              0           0
    ## 5                 1   0    0     1     0    0              0           0
    ## 6                 1   1    0     0     0    0              0           0
    ##   Friends difficultFinancial supportSystem postalCode
    ## 1       0                 No           Yes        L1X
    ## 2       0                 No           Yes        L3R
    ## 3       0                 No            No        L4Z
    ## 4       0                 No           Yes        L5N
    ## 5       0                 No            No        l5R
    ## 6       0                 No           Yes    L-T -G-
    ##               employmentStatus    sex primaryLanguage           ageRange
    ## 1                      Retired Female         English        65-69 years
    ## 2                      Retired   Male                        65-69 years
    ## 3 Unemployed, looking for work   Male         English        65-69 years
    ## 4           Employed full-time   Male         English        55-59 years
    ## 5           Employed full-time Female                 Less than 54 years
    ## 6           Employed full-time Female         Spanish        55-59 years
    ##   minAgeRange
    ## 1          65
    ## 2          65
    ## 3          65
    ## 4          55
    ## 5          54
    ## 6          55

Create a Venn Diagram
---------------------

### Prep the Data for a Venn Diagram

Venneuler takes data in the form of a two column data frame. The first
column represents the record identifier, and the second column
represents the set to include it in. Therefore, we may have records with
the same identifier repeated multiple times for every set they are part
of.

    vennSets <- sets %>%
      gather(transportation, binary,6:13) %>% # take all binary mappings and convert to be a the set indicator
      filter(binary == 1) %>% # only include set matches
      select(ID, transportation) %>% # only include ID and set category
      mutate(transportation = factor(transportation)) # set the transportation column as a factor

    dim(vennSets)

    ## [1] 12249     2

### Plot the Venn Diagram

    v <- venneuler(data.frame(vennSets))

    #Note that if you need to move around the labels so that they are not overlapping, you can use the new line breaks like the example below.
    #v$labels <- c("TTC", "Walk", "Drive", "Cycle\n\n\n", "\nTaxi", "Community Ride", "Wheel Trans", "Friends")

    par(cex = 0.7) 
    plot(v, main = "Modes of Senior Transportation (Toronto 2017 Survey)", cex.main = 1.5)
    grid.text(
      "@littlemissdata",
      x = 0.52,
      y = 0.15,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-6-1.png)

Plot the UpsetR Chart
---------------------

There is no need to reformat the data because it is already in the form
of a binary matrix. If your data was in the form of a list such as
above, you could transform it to a matrix using dplyr. I did this
previously by adding a helper column to set the binary value as 1 for
all rows in the list. You can then spread the column to create the
binary matrix. You will also need to replace the NA’s with 0’s. In this
case, the code would be as follows.

      sets %>% 
      add_column(ID=1) %>% #a helper column for the spread
      spread (transportation, ID) #spread the data to binary columns
      
      #NA replacement
      sets[is.na(sets)] <- 0
      
      

    upset(sets,
      nsets = 10, number.angles = 30, point.size = 3.5, line.size = 2,
      mainbar.y.label = "Modes of Senior Transportation (Toronto 2017 Survey)", sets.x.label = "Total Participants"
    )
    grid.text(
      "@littlemissdata",
      x = 0.90,
      y = 0.05,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-7-1.png)

Round 3: Exploring Information About the Sets!
==============================================

Now that we’ve visualized the relative sizes of the sets and their
intersection, we may care to know more about the sets.

Explore Extra Context Within a Venn Diagram
-------------------------------------------

Not available. This is the point in our journey when venn diagrams start
to fall out of the competition. While there are a few things that we
could do to bring in context about other areas of the data set, they are
not great options. For example, we could shade the circles according to
an aggregate value of another column (ie % working), but it would likely
be difficult to digest and not worth the effort.

### Explore Extra Context Within an UpsetR Chart

#### Highlight Focus Areas Within Chart With “Queries”"

A
[query](https://cran.r-project.org/web/packages/UpSetR/vignettes/queries.html)
is simply a subset of the data that you want to highlight or reference
later. The graph may contain multiple queries and that is why the
queries are defined in a list.

**Highlight Seniors Who Both Walk and Cycle Using “Query=Intersects”**

We will highlight all active seniors by looking for the intersection of
“Cycle” and “Walk” transportation modes.

    upset(sets,
      query.legend = "bottom", nsets = 10, number.angles = 30, point.size = 3.5, line.size = 2,
      mainbar.y.label = "Modes of Senior Transportation (Toronto 2017 Survey)", sets.x.label = "Total Participants", 
      queries = list(
      list(
        query = intersects,
        params = list("Cycle", "Walk"), 
        color = "#Df5286", 
        active = T,
        query.name = "Physically Active Transportation"
      )
      )
    )
    grid.text(
      "@littlemissdata",
      x = 0.90,
      y = 0.05,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-8-1.png)

**Highlight Seniors Who Exercise 1x/Week or Less Using
“Query=Elements”**

    upset(sets,
      query.legend = "bottom", nsets = 10, number.angles = 30, point.size = 3.5, line.size = 2,
      mainbar.y.label = "Modes of Senior Transportation (Toronto 2017 Survey)", sets.x.label = "Total Participants", 
      queries = list(
      list(
        query = elements,
        params = list("physicalActivityPerMonth", 0,4),
        color = "#Df5286", 
        active = T,
        query.name = "Physically Active 1x/Week or Less"
      )
      )
    )
    grid.text(
      "@littlemissdata",
      x = 0.90,
      y = 0.05,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-9-1.png)

### Provide Context with Additional Graphs Called “Attribute Plots”

**Display an in context box plot of age for each set using
boxplot.summary() function**

Attribute plots are an awesome way of displaying information about the
rest of the data set within the context of the sets or the queries
previously defined. More information can be found in the [excellent
package
tutorial.](https://cran.r-project.org/web/packages/UpSetR/vignettes/queries.html)

Note that you can adjust the graph ratios with the function:
`mb.ratio() function`

    upset(sets,
      query.legend = "bottom", nsets = 10, number.angles = 30, point.size = 3.5, line.size = 2,  
      queries = list(
      list(
        query = elements,
        params = list("physicalActivityPerMonth", 0,4),
        color = "#Df5286", 
        active = T,
        query.name = "Physically Active 1x/Week or Less"
      )
      ), 
      boxplot.summary = c("minAgeRange")
    )
    grid.text(
      "@littlemissdata",
      x = 0.90,
      y = 0.05,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-10-1.png) **Using
“Attribute Plots” Display In-Context Aggregate Statistics for Other
Columns and Highlight with Existing Queries**

We can use histograms, scatter plots etc.

    upset(sets,
      query.legend = "bottom", nsets = 10, number.angles = 30, point.size = 3.5, line.size = 2,
      mainbar.y.label = "Modes of Senior Transportation (Toronto 2017 Survey)", sets.x.label = "Total Participants", 
      queries = list(
      list(
        query = elements,
        params = list("physicalActivityPerMonth", 0,4),
        color = "#Df5286", 
        active = T,
        query.name = "Physically Active 1x/Week or Less"
      )
      ), 
      attribute.plots = list(gridrows = 50, 
        plots = list(list(plot = histogram, x = "volunteerPerMonth", queries = T), 
                     list(plot = histogram, x = "minAgeRange", queries = T), 
                     list(plot = scatter_plot, x = "minAgeRange", y="volunteerPerMonth", queries = F)
      ), 
    ncols = 3
    ) 
    )
    grid.text(
      "@littlemissdata",
      x = 0.9,
      y = 0.02,
      gp = gpar(
        fontsize = 10,
        fontface = 3
      )
    )

![](README_files/figure-markdown_strict/unnamed-chunk-11-1.png)

### Display Information About the Categories With “Set Metadata”

Metadata is a great way of displaying information about the core sets,
not including intersections. You can find out more information in the
great package documentation
[here](https://cran.r-project.org/web/packages/UpSetR/vignettes/set.metadata.plots.html).
There are essentially two steps: - Prepare the metadata - before any
summarization is displayed, we need to create a data frame which
contains the summary statistics about the sets that we want to display.
- Display the metadata graphs - self explanatory.

#### Prepare the Metadata

    aggregate <- sets %>% 
      gather(transportation, binary,6:13) %>% 
      filter(binary == 1) %>% # only include set matches
      group_by(transportation) %>%  #get summary stats per transportation category
      summarize(physicalActivityPerMonth = mean(physicalActivityPerMonth))
      
    aggregate

    ## # A tibble: 8 x 2
    ##   transportation physicalActivityPerMonth
    ##   <chr>                             <dbl>
    ## 1 Community Ride                     14.0
    ## 2 Cycle                              22.9
    ## 3 Drive                              16.2
    ## 4 Friends                            16.1
    ## 5 Taxi                               17.3
    ## 6 TTC                                18.5
    ## 7 Walk                               20.2
    ## 8 Wheel Trans                        12.7

#### Display the Metadata

    upset(sets, set.metadata = list(data = aggregate, plots = list(list(type = "hist", 
        column = "physicalActivityPerMonth", assign = 50))))

![](README_files/figure-markdown_strict/unnamed-chunk-13-1.png)

    sessionInfo() 

    ## R version 3.6.0 (2019-04-26)
    ## Platform: x86_64-apple-darwin15.6.0 (64-bit)
    ## Running under: macOS  10.15.2
    ## 
    ## Matrix products: default
    ## BLAS:   /Library/Frameworks/R.framework/Versions/3.6/Resources/lib/libRblas.0.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/3.6/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## attached base packages:
    ## [1] grid      stats     graphics  grDevices utils     datasets  methods  
    ## [8] base     
    ## 
    ## other attached packages:
    ##  [1] venneuler_1.1-0    forcats_0.4.0      stringr_1.4.0     
    ##  [4] dplyr_0.8.3        purrr_0.3.3        readr_1.3.1       
    ##  [7] tidyr_1.0.0        tibble_2.1.3       ggplot2_3.3.0.9000
    ## [10] tidyverse_1.3.0    UpSetR_1.4.0       rJava_0.9-11      
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] tidyselect_0.2.5 xfun_0.12        haven_2.2.0      lattice_0.20-38 
    ##  [5] colorspace_1.4-1 generics_0.0.2   vctrs_0.2.2      htmltools_0.3.6 
    ##  [9] yaml_2.2.1       utf8_1.1.4       rlang_0.4.4      pillar_1.4.3    
    ## [13] withr_2.1.2      glue_1.3.1       DBI_1.0.0        dbplyr_1.4.2    
    ## [17] modelr_0.1.5     readxl_1.3.1     lifecycle_0.1.0  plyr_1.8.5      
    ## [21] munsell_0.5.0    gtable_0.3.0     cellranger_1.1.0 rvest_0.3.5     
    ## [25] evaluate_0.14    labeling_0.3     knitr_1.24       fansi_0.4.1     
    ## [29] broom_0.5.2      Rcpp_1.0.3       scales_1.1.0     backports_1.1.5 
    ## [33] jsonlite_1.6.1   farver_2.0.3     fs_1.3.1         gridExtra_2.3   
    ## [37] hms_0.5.3        digest_0.6.24    stringi_1.4.6    cli_2.0.1       
    ## [41] tools_3.6.0      magrittr_1.5     crayon_1.3.4     pkgconfig_2.0.3 
    ## [45] ellipsis_0.3.0   xml2_1.2.2       reprex_0.3.0     lubridate_1.7.4 
    ## [49] rstudioapi_0.11  assertthat_0.2.1 rmarkdown_1.15   httr_1.4.1      
    ## [53] R6_2.4.1         nlme_3.1-140     compiler_3.6.0
