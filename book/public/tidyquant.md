# Timeseries with tidyquant {#tidyquant}

![](images/banners/banner_tidyquant.png)
*This chapter originated as a community contribution created by [	naotominakawa](https://github.com/naotominakawa){target="_blank"}*

*This page is a work in progress. We appreciate any input you may have. If you would like to help improve this page, consider [contributing to our repo](contribute.html).*



## Overview
This section covers how to use the `tidyquant` package to conduct timeseries analysis.

## What is tidyquant?

`tidyquant` is an one-stop shop for financial analysis. It is suitable for analyzing timeseries data, such as financial and economic data. `tidyquant` connects to various data sources such as Yahoo! Finance, Morning Star, Bloomberg market data, etc. It also behaves well with other `Tidyverse` packages.

## Installing tidyquant
To install `tidyquant`, you can run the following code:

```r
# install.packages("tidyquant")
library(tidyquant)
```

If you want to see which functions are available, you can run the following:

```r
# to see which functions are available
tq_transmute_fun_options()
```

```
## $zoo
##  [1] "rollapply"          "rollapplyr"         "rollmax"           
##  [4] "rollmax.default"    "rollmaxr"           "rollmean"          
##  [7] "rollmean.default"   "rollmeanr"          "rollmedian"        
## [10] "rollmedian.default" "rollmedianr"        "rollsum"           
## [13] "rollsum.default"    "rollsumr"          
## 
## $xts
##  [1] "apply.daily"     "apply.monthly"   "apply.quarterly"
##  [4] "apply.weekly"    "apply.yearly"    "diff.xts"       
##  [7] "lag.xts"         "period.apply"    "period.max"     
## [10] "period.min"      "period.prod"     "period.sum"     
## [13] "periodicity"     "to_period"       "to.daily"       
## [16] "to.hourly"       "to.minutes"      "to.minutes10"   
## [19] "to.minutes15"    "to.minutes3"     "to.minutes30"   
## [22] "to.minutes5"     "to.monthly"      "to.period"      
## [25] "to.quarterly"    "to.weekly"       "to.yearly"      
## 
## $quantmod
##  [1] "allReturns"      "annualReturn"    "ClCl"           
##  [4] "dailyReturn"     "Delt"            "HiCl"           
##  [7] "Lag"             "LoCl"            "LoHi"           
## [10] "monthlyReturn"   "Next"            "OpCl"           
## [13] "OpHi"            "OpLo"            "OpOp"           
## [16] "periodReturn"    "quarterlyReturn" "seriesAccel"    
## [19] "seriesDecel"     "seriesDecr"      "seriesHi"       
## [22] "seriesIncr"      "seriesLo"        "weeklyReturn"   
## [25] "yearlyReturn"   
## 
## $TTR
##  [1] "adjRatios"          "ADX"                "ALMA"              
##  [4] "aroon"              "ATR"                "BBands"            
##  [7] "CCI"                "chaikinAD"          "chaikinVolatility" 
## [10] "CLV"                "CMF"                "CMO"               
## [13] "DEMA"               "DonchianChannel"    "DPO"               
## [16] "DVI"                "EMA"                "EMV"               
## [19] "EVWMA"              "GMMA"               "growth"            
## [22] "HMA"                "KST"                "lags"              
## [25] "MACD"               "MFI"                "momentum"          
## [28] "OBV"                "PBands"             "ROC"               
## [31] "rollSFM"            "RSI"                "runCor"            
## [34] "runCov"             "runMAD"             "runMax"            
## [37] "runMean"            "runMedian"          "runMin"            
## [40] "runPercentRank"     "runSD"              "runSum"            
## [43] "runVar"             "SAR"                "SMA"               
## [46] "SMI"                "SNR"                "stoch"             
## [49] "TDI"                "TRIX"               "ultimateOscillator"
## [52] "VHF"                "VMA"                "volatility"        
## [55] "VWAP"               "VWMA"               "wilderSum"         
## [58] "williamsAD"         "WMA"                "WPR"               
## [61] "ZigZag"             "ZLEMA"             
## 
## $PerformanceAnalytics
## [1] "Return.annualized"        "Return.annualized.excess"
## [3] "Return.clean"             "Return.cumulative"       
## [5] "Return.excess"            "Return.Geltner"          
## [7] "zerofill"
```

## Single timeseries
Obtain historical data for single stock (for example, Google):

```r
# get historical data for single stock. e.g. google
tq_get("GOOGL", get="stock.prices")
```

```
## # A tibble: 2,680 x 7
##    date        open  high   low close   volume adjusted
##    <date>     <dbl> <dbl> <dbl> <dbl>    <dbl>    <dbl>
##  1 2009-01-02  154.  161.  153.  161.  7213700     161.
##  2 2009-01-05  161.  166.  158.  164.  9768200     164.
##  3 2009-01-06  167.  171.  163.  167. 12837500     167.
##  4 2009-01-07  164.  166.  160.  161.  8980000     161.
##  5 2009-01-08  159.  163.  159.  163.  7194100     163.
##  6 2009-01-09  164.  164.  157.  158.  8672300     158.
##  7 2009-01-12  158.  160.  155.  157.  6601900     157.
##  8 2009-01-13  156.  160.  155.  157.  8856100     157.
##  9 2009-01-14  155.  157.  149.  151. 10924800     151.
## 10 2009-01-15  149.  152.  144.  150. 11857100     150.
## # … with 2,670 more rows
```

Calculate monthly return of single stock:

```r
# calculate monthly return of single stock
tq_get(c("GOOGL"), get="stock.prices") %>%
  tq_transmute(select=adjusted,
               mutate_fun=periodReturn,
               period="monthly",
               col_rename = "monthly_return")
```

```
## # A tibble: 128 x 2
##    date       monthly_return
##    <date>              <dbl>
##  1 2009-01-30        0.0536 
##  2 2009-02-27       -0.00160
##  3 2009-03-31        0.0298 
##  4 2009-04-30        0.138  
##  5 2009-05-29        0.0537 
##  6 2009-06-30        0.0104 
##  7 2009-07-31        0.0509 
##  8 2009-08-31        0.0420 
##  9 2009-09-30        0.0740 
## 10 2009-10-30        0.0812 
## # … with 118 more rows
```

Create a line chart of the *closing price* for single stock:

```r
# showing closing price for single stock
tq_get(c("GOOGL"), get="stock.prices") %>%
  ggplot(aes(date, close)) +
  geom_line()
```

<img src="tidyquant_files/figure-html/unnamed-chunk-5-1.png" width="672" />

Create a line chart of the *monthly return* for single stock:

```r
# showing monthly return for single stock
tq_get(c("GOOGL"), get="stock.prices") %>%
  tq_transmute(select=adjusted,
               mutate_fun=periodReturn,
               period="monthly",
               col_rename = "monthly_return") %>%
  ggplot(aes(date, monthly_return)) +
  geom_line()
```

<img src="tidyquant_files/figure-html/unnamed-chunk-6-1.png" width="672" />

## Multiple timeseries
Obtain historical data for multiple stocks (for example, GAFA):

```r
# get historical data for multiple stocks. e.g. GAFA
tq_get(c("GOOGL","AMZN","FB","AAPL"), get="stock.prices")
```

```
## # A tibble: 9,869 x 8
##    symbol date        open  high   low close   volume adjusted
##    <chr>  <date>     <dbl> <dbl> <dbl> <dbl>    <dbl>    <dbl>
##  1 GOOGL  2009-01-02  154.  161.  153.  161.  7213700     161.
##  2 GOOGL  2009-01-05  161.  166.  158.  164.  9768200     164.
##  3 GOOGL  2009-01-06  167.  171.  163.  167. 12837500     167.
##  4 GOOGL  2009-01-07  164.  166.  160.  161.  8980000     161.
##  5 GOOGL  2009-01-08  159.  163.  159.  163.  7194100     163.
##  6 GOOGL  2009-01-09  164.  164.  157.  158.  8672300     158.
##  7 GOOGL  2009-01-12  158.  160.  155.  157.  6601900     157.
##  8 GOOGL  2009-01-13  156.  160.  155.  157.  8856100     157.
##  9 GOOGL  2009-01-14  155.  157.  149.  151. 10924800     151.
## 10 GOOGL  2009-01-15  149.  152.  144.  150. 11857100     150.
## # … with 9,859 more rows
```

Create a multiple line chart of the closing prices of multiple stocks (again, GAFA). We can show each stock in a different color on the same graph:

```r
# Create a multiple line chart of the closing prices of the four stocks,
# showing each stock in a different color on the same graph.
tq_get(c("GOOGL","AMZN","FB","AAPL"), get="stock.prices") %>%
  ggplot(aes(date, close, color=symbol)) +
  geom_line()
```

<img src="tidyquant_files/figure-html/unnamed-chunk-8-1.png" width="672" />

Transform the data so each stock begins at 100 and replot (Standardize the data so that we can compare timeseries):

```r
# Create a multiple line chart of the closing prices of the four stocks,
# showing each stock in a different color on the same graph.
# Transform the data so each stock begins at 100 and replot.
tq_get(c("GOOGL","AMZN","FB","AAPL"), get="stock.prices") %>%
  group_by(symbol) %>%
  mutate(close = 100*close/first(close)) %>%
  ggplot(aes(date, close, color=symbol)) +
  geom_line()
```

<img src="tidyquant_files/figure-html/unnamed-chunk-9-1.png" width="672" />

Calculate *monthly return* of multiple stocks (again, GAFA):

```r
# calculate monthly return of multiple stocks
tq_get(c("GOOGL","AMZN","FB","AAPL"), get="stock.prices") %>%
  group_by(symbol) %>%
  tq_transmute(select=adjusted,
               mutate_fun=periodReturn,
               period="monthly",
               col_rename = "monthly_return")
```

```
## # A tibble: 472 x 3
## # Groups:   symbol [4]
##    symbol date       monthly_return
##    <chr>  <date>              <dbl>
##  1 GOOGL  2009-01-30        0.0536 
##  2 GOOGL  2009-02-27       -0.00160
##  3 GOOGL  2009-03-31        0.0298 
##  4 GOOGL  2009-04-30        0.138  
##  5 GOOGL  2009-05-29        0.0537 
##  6 GOOGL  2009-06-30        0.0104 
##  7 GOOGL  2009-07-31        0.0509 
##  8 GOOGL  2009-08-31        0.0420 
##  9 GOOGL  2009-09-30        0.0740 
## 10 GOOGL  2009-10-30        0.0812 
## # … with 462 more rows
```

Create a multiple line chart of monthly return of the four stocks. Again, we can show each stock in a different color on the same graph:

```r
# Create a multiple line chart of monthly return of the four stocks,
# showing each stock in a different color on the same graph
tq_get(c("GOOGL","AMZN","FB","AAPL"), get="stock.prices") %>%
  group_by(symbol) %>%
  tq_transmute(select=adjusted,
               mutate_fun=periodReturn,
               period="monthly",
               col_rename = "monthly_return") %>%
  ggplot(aes(date, monthly_return, color=symbol)) +
  geom_line()
```

<img src="tidyquant_files/figure-html/unnamed-chunk-11-1.png" width="672" />

## External Resources
- [tidyquant CRAN doc](https://cran.r-project.org/web/packages/tidyquant/vignettes/TQ00-introduction-to-tidyquant.html){target="_blank"}: formal documentation on the package
- [tidyquant Github repo](https://github.com/business-science/tidyquant){target="_blank"}: Github repository for the `tidyquant` package with a great README
