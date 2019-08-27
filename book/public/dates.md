# Dates in R {#dates}

![](images/banners/banner_dates.png)

## Introduction

Working with dates and time can be very frustrating.  In general, work with the least cumbersome class. That means if your variable is *years*, store it as an integer; there's no reason to use a date or date-time class. If your variable does not involve time, use the `Date` class in R.

## Converting to `Date` class

 You can convert character data to `Date` class with `as.Date()`:


```r
dchar <- "2018-10-12"
ddate <- as.Date(dchar)
```

Note that the two appear the same, although the class is different:


```r
dchar
```

```
## [1] "2018-10-12"
```

```r
ddate
```

```
## [1] "2018-10-12"
```

```r
class(dchar)
```

```
## [1] "character"
```

```r
class(ddate)
```

```
## [1] "Date"
```

If the date is not in YYYY-MM-DD or YYYY/MM/DD form, you will need to specify the format to convert to `Date` class, using conversion specifications that begin with `%`, such as:


```r
as.Date("Thursday, January 6, 2005", format = "%A, %B %d, %Y")
```

```
## [1] "2005-01-06"
```

For a list of the conversion specifications available in R, see `?strptime`.

The tidyverse **lubridate** makes it easy to convert dates that are not in standard format with `ymd()`, `ydm()`, `mdy()`, `myd()`, `dmy()`, and `dym()` (among many other useful date-time functions):


```r
lubridate::mdy("April 13, 1907")
```

```
## [1] "1907-04-13"
```

Try `as.Date("April 13, 1907")` and you will see the benefit of using a **lubridate** function.

## Working with `Date` Class

It is well worth the effort to convert to `Date` class, because there's a lot you can do with dates in a `Date` class that you can't do if you store the dates as character data.

Number of days between dates:


```r
as.Date("2017-11-02") - as.Date("2017-01-01")
```

```
## Time difference of 305 days
```

Compare dates:

```r
as.Date("2017-11-12") > as.Date("2017-3-3")
```

```
## [1] TRUE
```

Note that `Sys.Date()` returns today's date as a `Date` class:


```r
Sys.Date()
```

```
## [1] "2019-08-27"
```

```r
class(Sys.Date())
```

```
## [1] "Date"
```

R has functions to pull particular pieces of information from a date:


```r
today <- Sys.Date()
weekdays(today)
```

```
## [1] "Tuesday"
```

```r
weekdays(today, abbreviate = TRUE)
```

```
## [1] "Tue"
```

```r
months(today)
```

```
## [1] "August"
```

```r
months(today, abbreviate = TRUE)
```

```
## [1] "Aug"
```

```r
quarters(today)
```

```
## [1] "Q3"
```

The **lubridate** package provides additional functions to extract information from a date:


```r
today <- Sys.Date()
lubridate::year(today)
```

```
## [1] 2019
```

```r
lubridate::yday(today)
```

```
## [1] 239
```

```r
lubridate::month(today)
```

```
## [1] 8
```

```r
lubridate::month(today, label = TRUE)
```

```
## [1] Aug
## 12 Levels: Jan < Feb < Mar < Apr < May < Jun < Jul < Aug < Sep < ... < Dec
```

```r
lubridate::mday(today)
```

```
## [1] 27
```

```r
lubridate::week(today)
```

```
## [1] 35
```

```r
lubridate::wday(today)
```

```
## [1] 3
```

## Plotting with a `Date` class variable

Both base R graphics and **ggplot2** "know" how to work with a `Date` class variable, and label the axes properly:

### base R


```r
df <- read.csv("data/mortgage.csv")
df$DATE <- as.Date(df$DATE)
plot(df$DATE, df$X5.1.ARM, type = "l") # on the order of years
```

<img src="dates_files/figure-html/unnamed-chunk-10-1.png" width="672" />

```r
plot(df$DATE[1:30], df$X5.1.ARM[1:30], type = "l") # switch to months
```

<img src="dates_files/figure-html/unnamed-chunk-10-2.png" width="672" />

Note the the change in x-axis labels in the second graph.

### ggplot2


```r
# readr 
library(tidyverse)
```

Note that unlike base R`read.csv()`, `readr::read_csv()` automatically reads DATE in as a `Date` class since it's in YYYY-MM-DD format:


```r
df <- readr::read_csv("data/mortgage.csv")
```

```
## Parsed with column specification:
## cols(
##   DATE = col_date(format = ""),
##   `5/1 ARM` = col_double(),
##   `15 YR FIXED` = col_double(),
##   `30 YR FIXED` = col_double()
## )
```

```r
g <- ggplot(df, aes(DATE, `30 YR FIXED`)) + 
  geom_line() + 
  theme_grey(14)

g
```

<img src="dates_files/figure-html/unnamed-chunk-12-1.png" width="672" />

```r
ggplot(df %>% filter(DATE < as.Date("2006-01-01")), 
       aes(DATE, `30 YR FIXED`)) + 
  geom_line() + 
  theme_grey(14)
```

<img src="dates_files/figure-html/unnamed-chunk-12-2.png" width="672" />

Again, when the data is filtered, the x-axis labels switch from years to months.

#### Breaks, limits, labels

We can control the x-axis breaks, limits, and labels with `scale_x_date()`:



```r
library(lubridate)
g + scale_x_date(limits = c(ymd("2008-01-01"), ymd("2008-12-31"))) +
  ggtitle("limits = c(ymd(\"2008-01-01\"), ymd(\"2008-12-31\"))")
```

<img src="dates_files/figure-html/unnamed-chunk-13-1.png" width="672" />

```r
g + scale_x_date(date_breaks = "4 years") +
  ggtitle("scale_x_date(date_breaks = \"4 years\")")
```

<img src="dates_files/figure-html/unnamed-chunk-13-2.png" width="672" />

```r
g + scale_x_date(date_labels = "%Y-%m") +
  ggtitle("scale_x_date(date_labels = \"%Y-%m\")")
```

<img src="dates_files/figure-html/unnamed-chunk-13-3.png" width="672" />

(Yes, even in the tidyverse we cannot completely escape the `%` conversion specification notation.  Remember `?strptime` for help.)

#### Annotations

We can use `geom_vline()` with `annotate()` to mark specific events in a time series:


```r
ggplot(df, aes(DATE, `30 YR FIXED`)) + 
  geom_line() +
  geom_vline(xintercept = ymd("2008-09-29"), color = "blue") +
  annotate("text", x = ymd("2008-09-29"), y = 3.75, 
           label = " Market crash\n 9/29/08", color = "blue", 
           hjust = 0) +
  scale_x_date(limits = c(ymd("2008-01-01"), ymd("2009-12-31")),
               date_breaks = "1 year", 
               date_labels = "%Y") + 
  theme_grey(16) +
  ggtitle("`geom_vline()` with `annotate()`")
```

<img src="dates_files/figure-html/unnamed-chunk-14-1.png" width="672" />

