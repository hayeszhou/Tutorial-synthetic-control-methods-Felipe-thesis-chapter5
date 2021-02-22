Long Run vs Short Run Decompositions in R
================
Carlos Mendez

<style>
h1.title {font-size: 18pt; color: DarkBlue;} 
body, h1, h2, h3, h4 {font-family: "Palatino", serif;}
body {font-size: 12pt;}
/* Headers */
h1,h2,h3,h4,h5,h6{font-size: 14pt; color: #00008B;}
body {color: #333333;}
a, a:hover {color: #8B3A62;}
pre {font-size: 12px;}
</style>

Suggested Citation:

> Mendez C. (2020). Long Run vs Short Run Decompositions in R: The HP
> filter vs the Hamilton filter. R Studio/RPubs. Available at
> <https://rpubs.com/quarcs-lab/long-run-filters>

This work is licensed under the Creative Commons Attribution-Share Alike
4.0 International License. ![](License.png)

# Set parameters of the program

  - Name of the series

<!-- end list -->

``` r
seriesName <- "RGDPNAIDA666NRUG"
```

Code examples for other series

  - Total GDP of Japan: “JPNRGDPEXP”
  - GDP per capita of Japan: “RGDPCHJPA625NUPN”
  - GPD per capita of Bolivia: “NYGDPPCAPKDBOL”
  - Total GDP of Bolivia: “RGDPNABOA666NRUG”
  - Total GDP of Indonesia: “RGDPNAIDA666NRUG”

# Load libraries

``` r
library(mFilter)
library(quantmod)
library(dplyr)
library(ggplot2)
library(dygraphs)
library(xts)
library(neverhpfilter)

# Change the presentation of decimal numbers to 4 and avoid scientific notation
options(prompt="R> ", digits=3, scipen=999)
```

# Import data

``` r
seriesName <- getSymbols(seriesName, src="FRED", auto.assign = FALSE)
```

    ## 'getSymbols' currently uses auto.assign=TRUE by default, but will
    ## use auto.assign=FALSE in 0.5-0. You will still be able to use
    ## 'loadSymbols' to automatically load data. getOption("getSymbols.env")
    ## and getOption("getSymbols.auto.assign") will still be checked for
    ## alternate defaults.
    ## 
    ## This message is shown once per session and may be disabled by setting 
    ## options("getSymbols.warning4.0"=FALSE). See ?getSymbols for details.

``` r
periodicity(seriesName)
```

    ## Yearly periodicity from 1960-01-01 to 2019-01-01

# Transform the data

  - Take the log of the series

<!-- end list -->

``` r
seriesName <- log(seriesName)
```

# Plot evolution of the variable

``` r
dygraph(seriesName) %>% 
  dyRangeSelector()
```

![](tutorial-long-run-filters_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

# Apply the HP filter

``` r
seriesName_filtered_HP <- hpfilter(seriesName, 
                                  freq = 6.25      
                                     )
```

## Plot the HP filter

### Long-run trend

Create matrix of actual, trend , and cycle values

``` r
actual  <- seriesName_filtered_HP[["x"]]
trendHP <- seriesName_filtered_HP[["trend"]]
cycleHP <- actual - trendHP

colnames(actual)   <- c("actual")
colnames(trendHP)  <- c("trendHP")
colnames(cycleHP)  <- c("cycleHP")

actual_and_trend <- cbind(actual, trendHP)
```

``` r
dygraph(actual_and_trend[,1:2]) %>%
  dyRangeSelector()
```

![](tutorial-long-run-filters_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

### Short-run fluctuations

``` r
dygraph(cycleHP)  %>% 
  dyRangeSelector()
```

![](tutorial-long-run-filters_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

# Apply the Hamilton filter

``` r
seriesName_filtered_Hamilton <- yth_filter(seriesName, 
                                           h = 2, 
                                           p = 4, 
                                           output = c("x", "trend", "cycle"))
```

## Plot the Hamiltion filter

### Long-run trend

Rename columns

``` r
colnames(seriesName_filtered_Hamilton)  <- c("actual",
                                             "trendHamilton",
                                             "cycleHamilton")
```

``` r
dygraph(seriesName_filtered_Hamilton[,1:2])  %>% 
  dyRangeSelector()
```

![](tutorial-long-run-filters_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

### Short-run fluctuation

``` r
dygraph(seriesName_filtered_Hamilton[,3])  %>% 
  dyRangeSelector()
```

![](tutorial-long-run-filters_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

# Run it in the cloud

Tip: Copy and paste this link another tab of your browser.

<https://rstudio.cloud/project/25043>

Or simply
<a href="https://rstudio.cloud/project/25043" target="_blank">ClickHERE</a>

# References

  - [Schüler, Y., 2018. On the cyclical properties of Hamilton’s
    regression filter (No. 03/2018). Deutsche
    Bundesbank.](https://www.econstor.eu/bitstream/10419/174891/1/1014338883.pdf)

  - <http://past.rinfinance.com/agenda/2018/JustinShea.pdf>

  - <https://www.r-econometrics.com/timeseries/economic-cycle-extraction/>

  - <https://www.datacamp.com/community/blog/r-xts-cheat-sheet>

  - <https://rstudio.github.io/dygraphs/>
