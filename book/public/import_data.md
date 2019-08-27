# Importing Data {#import}

![](images/banners/banner_import.png)
*This chapter originated as a community contribution created by [	ZhangZhida](https://github.com/ZhangZhida){target="_blank"}*

*This page is a work in progress. We appreciate any input you may have. If you would like to help improve this page, consider [contributing to our repo](contribute.html).*

## Overview

This section covers how to import data from built-in R sources, local files, web sources and databases. 

## Import built-in dataset

R comes with quite a lot of built-in datasets, which R users can play around with. You are probably familiar with many of the built-in datasets like `iris`, `mtcars`, `beavers`, `dataset`, etc. Since datasets are preloaded, we can manipulate them directly. To see a full list of built-in R datasets and their descriptions, please refer to [The R Datasets Package](https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/00Index.html){target="_blank"}. We can also run `data()` to view the full list.

After we find the dataset we want, we can use the function `data(package_name)` to load the built-in dataset into our workspace. For example, here we want to load the `iris` dataset:


```r
# load data
data("iris")
head(iris)
```

```
##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
## 1          5.1         3.5          1.4         0.2  setosa
## 2          4.9         3.0          1.4         0.2  setosa
## 3          4.7         3.2          1.3         0.2  setosa
## 4          4.6         3.1          1.5         0.2  setosa
## 5          5.0         3.6          1.4         0.2  setosa
## 6          5.4         3.9          1.7         0.4  setosa
```

Besides the datasets above, there are other datasets within specific R packages. To see a full list of data in various packages, run `??datasets` in the RStudio console.
For exmaple, say we want to load the `diamonds` dataset from the `ggplot2` package. To load the `diamonds` data, first we need to load the `ggplot2` library:


```r
library(ggplot2)

data("diamonds")
head(diamonds)
```

```
## # A tibble: 6 x 10
##   carat cut       color clarity depth table price     x     y     z
##   <dbl> <ord>     <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
## 1 0.23  Ideal     E     SI2      61.5    55   326  3.95  3.98  2.43
## 2 0.21  Premium   E     SI1      59.8    61   326  3.89  3.84  2.31
## 3 0.23  Good      E     VS1      56.9    65   327  4.05  4.07  2.31
## 4 0.290 Premium   I     VS2      62.4    58   334  4.2   4.23  2.63
## 5 0.31  Good      J     SI2      63.3    58   335  4.34  4.35  2.75
## 6 0.24  Very Good J     VVS2     62.8    57   336  3.94  3.96  2.48
```

## Import local data

### Import text file

The function `read.table()` is the most general function for reading text files. To use this function, we need to specify how we read the file. In other words, we need to specify some basic parameters like `sep`, `header`, etc. `sep` represents the separator, and `header` is set to `TRUE` if we want to read the first line as the header information. Other parameters are also useful in different cases. For example, `na.strings` indicates strings should be regarded as NA values.


```r
df <- read.table("data/MusicIcecream.csv", sep=",", header=TRUE)
head(df)
```

```
##     Age   Favorite     Music Freq
## 1   old bubble gum classical    1
## 2   old bubble gum      rock    1
## 3   old     coffee classical    3
## 4   old     coffee      rock    1
## 5 young bubble gum classical    2
## 6 young bubble gum      rock    5
```

### Import CSV file
A Comma-Separated Values file (CSV) is a delimited text file that uses a comma to separate values. We can easily read a CSV file with built-in R functions. 

The `read.csv()` function provides two useful parameters. One is `header`, which can be set to `FALSE` if there is no header. The other is `sep`, which specifies the separator. For example, we can specify the separator to be `sep="\t` if the CSV file value is seperated by the tab character. The default value of `header` and `sep` are `TRUE` and `","`, respectively. 

`read.csv2()` is another function for reading CSV files. The difference between `read.csv()` and `read.csv2` is that, the former uses the tab `"\t"` as the separator, while the latter one uses the semicolon `";"`. This serves as an easy shortcut for different CSV formats used in different regions.

Let's see an example on reading a standard CSV file:

```r
df <- read.csv("data/MusicIcecream.csv")
head(df)
```

```
##     Age   Favorite     Music Freq
## 1   old bubble gum classical    1
## 2   old bubble gum      rock    1
## 3   old     coffee classical    3
## 4   old     coffee      rock    1
## 5 young bubble gum classical    2
## 6 young bubble gum      rock    5
```

A small note while reading multiple files: let R know your current directory by using `setwd()`. Then, you can read any file in this directory by directly using the name of the file, without specifying the location. 

### Import JSON file

A JSON file is a file that stores simple data structures and objects in JavaScript Object Notation (JSON) format, which is a standard data interchange format. For example, `{"name":"Vince", "age":23, "city":"New York"}` is an object with JSON format. In recent years, JSON has become the mainstream format to transfer data on websites. 

To read a JSON file, we can use the `jsonlite` package. The `jsonlite` package is a JSON parser/generator optimized for the web. Its main strength is that it implements a bidirectional mapping between JSON data and the most important R data types. In the example below, the argument `simplifyDataFrame = TRUE` will directly transform a list of JSON objects into a dataframe. If you want to know more about the arguments `simplifyVector` and `simplifyMatrix`, which provide flexible control on other R data formats to transform to, please refer to [Getting started with JSON and jsonlite](https://cran.r-project.org/web/packages/jsonlite/vignettes/json-aaquickstart.html){target="_blank"}.


```r
library(jsonlite)

# read JSON data
raw_json_data <- fromJSON(txt = "data/WaterConsumptionInNYC.json", simplifyDataFrame = TRUE)

# transform JSON to Data Frame
df <- as.data.frame(raw_json_data)
head(df)
```

```
##   new_york_city_population nyc_consumption_million_gallons_per_day
## 1                  7102100                                    1512
## 2                  7071639                                    1506
## 3                  7089241                                    1309
## 4                  7109105                                    1382
## 5                  7181224                                    1424
## 6                  7234514                                    1465
##   per_capita_gallons_per_person_per_day year
## 1                                   213 1979
## 2                                   213 1980
## 3                                   185 1981
## 4                                   194 1982
## 5                                   198 1983
## 6                                   203 1984
```

## Import web data

We can import data from the web, as long as we have the link to the dataset. Basically, there are two ways to import web data. One way is to scrape the dataset with the link before we import that data into the R workspace. The other way is to import the dataset from the web to our workspace directly. 

Let's take the example of `Water Consumption In The New York City`, which is on the [NYC Open Data website](https://data.cityofnewyork.us/Environment/Water-Consumption-In-The-New-York-City/ia2d-e54m){target="_blank}. 

### Scrape web data to local space/memory before importing it

To securely access the web data, we can use the `RCurl` package (for more resources, see [The RCurl Package](http://www.omegahat.net/RCurl/){target="_blank"}. To be more flexible, we can first download the data to local hard drive and then read them as local files. To download the file, we can use the commands like `curl` : [Downloading files with curl](http://www.compciv.org/recipes/cli/downloading-with-curl/){target="_blank"}, `wget` : [Wget Command Examples](https://www.rosehosting.com/blog/wget-command-examples/){target="_blank"}. You can also download it with the browser.



### Directly read web source into the workspace

We still use the water consumption data as an example. First, we specify the correct data URL, and then read the data just like reading the local dataset:


```r
# specify the URL link to the data source
url <- "https://data.cityofnewyork.us/api/views/ia2d-e54m/rows.csv"

# read the URL
df <- read.table(url, sep = ",", header = TRUE)
head(df)
```

## Import data from database

R provides packages to manipulate data from relational databases like PostgreSQL, MySQL, etc. One of those packages is `odbc` package, which is one database interface for communication between R and relational database management systems. More resources on package: [odbc](https://db.rstudio.com/odbc/){target="_blank"}. 

Before we connect to a local database, we must satisfy the requirement of the ODBC driver, through which our R package can communicate with the database. To get help on how to install ODBC driver on systems like Windows, Linux, MacOS, please refer to this document: [Install ODBC Driver](https://cran.r-project.org/web/packages/odbc/readme/README.html){target="_blank"}.

After we installed the ODBC driver, with `odbc` and `DBI` packages, we are able to manipulate the database. To read a table in the database, we usually take steps as follows. First, we build the connection to the database using `dbConnect()` function. Then, we can do some exploratory operations like listing all tables in the database. To query the data we want, we can send a SQL query into the database. Then we can retrieve the desired data and `dfFetch()` provides control on how many records to retrieve at a time. Finally, we finish reading and close the connection. 


```r
library(odbc)
library(DBI)

# build connection with database 
con <- dbConnect(odbc::odbc(),
  driver = "PostgreSQL Driver",
  database = "test_db",
  uid = "postgres",
  pwd = "password",
  host = "localhost",
  port = 5432)

# list all tables in the test_db database
dbListTables(con)

# read table test_table into Data Frame
data <- dbReadTable(con, "test_table")

# write an R Data Frame object to an SQL table
# here we write the built-in data mtcars to a new_table in DB
data <- dbWriteTable(con, "new_table", mtcars)

# SQL query
result <- dbSendQuery(con, "SELECT * FROM test_table")

# Retrieve the first 10 results
first_10 <- dbFetch(result, n = 10)
# Retrieve the rest of the results
rest <- dbFetch(result)

# close the connection
dbDisconnect(con)
```

## More resources

  - Import local file:
  [This R Data Import Tutorial Is Everything You Need](https://www.datacamp.com/community/tutorials/r-data-import-tutorial#Getting){target="_blank"}
  - Import JSON file: 
  [Getting started with JSON and jsonlite](https://cran.r-project.org/web/packages/jsonlite/vignettes/json-aaquickstart.html){target="_blank"}
  - Import web data:
  [The RCurl Package](http://www.omegahat.net/RCurl/){target="_blank"}
  - Import database file
  [Databases using R](https://db.rstudio.com/){target="_blank"}
  - Documentation on odbc package
  [odbc](https://db.rstudio.com/odbc/){target="_blank"}
  - Install ODBC Driver On Your System
  [Install ODBC Driver](https://cran.r-project.org/web/packages/odbc/readme/README.html){target="_blank"}
