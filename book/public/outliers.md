# Outliers {#outliers}

![](images/banners/banner_outliers.png)
*This chapter originated as a community contribution created by [	kiransaini](https://github.com/kiransaini){target="_blank"}*

*This page is a work in progress. We appreciate any input you may have. If you would like to help improve this page, consider [contributing to our repo](contribute.html).*

## Overview

This section covers what types of outliers are encountered in data and how to handle them.

## tl;dr

I want to see my outliers! 

Outliers are difficult to spot because judging a datapoint as an outlier depends on the data or model with which they are compared. It is important to detect outliers because they can distort predictions and affect the accuracy of the model. 

## What are outliers?

Outliers are noticeably far from the bulk of the data. They can be errors, genuine extreme values, rare values, unusual values, cases of special interest, or data from another source. Outliers on individual variables can be spotted using [boxplots](box.html) and bivariate outliers can be spotted using [scatterplots](scatter.html). There can also be higher dimensional outliers that are not outliers in lower dimensions. 

It is worth identifying outliers for a number of reasons. Bad outliers should always be corrected and many statistical methods may work poorly in presence of outliers, but genuine outlying values can be interesting in their own right. Let's have a look at the outliers of the 'carat' variable in the `diamonds` dataset:

<img src="outliers_files/figure-html/unnamed-chunk-1-1.png" width="672" />

## Types of Outliers

### Univariate Outliers
**Univariate outliers** are outlying along one dimension. The best-known approach for an initial look at the data is to use boxplots. [Tukey](https://en.wikipedia.org/wiki/John_Tukey){target="_blank"} suggests marking individual cases as outliers if they are more than 1.5 IQR (the interquartile range) outside the hinges (basically the quartiles). Outliers may change if they are grouped by another variable. Let's have a look at outliers on the Sepal Width variable in the `iris` dataset, both when the data is grouped by Species and when it is not. The outliers are clearly different:

```r
p <- ggplot(iris, aes(x=Species, y=Sepal.Width)) + 
  geom_boxplot(color="black", fill="lightblue") + 
  ggtitle("Boxplot for Sepal Width grouped by Species in iris dataset")
p
```

<img src="outliers_files/figure-html/unnamed-chunk-2-1.png" width="672" />

```r
p <- ggplot(iris, aes(y=Sepal.Width)) + 
  geom_boxplot(color="black", fill="lightblue") + 
  ggtitle("Boxplot for Sepal Width in iris dataset")
p
```

<img src="outliers_files/figure-html/unnamed-chunk-2-2.png" width="672" />

### Multivariate Outliers 
**Multivariate outliers** are outlying along more than one dimension. Scatterplots and parallel coordinate plots are useful for visualizing multivariate outliers. You could regard points as outliers that are far from the mass of the data, or you could regard points as outliers that do not fit the smooth model well. Some points are outliers on both criteria. Let's have a look at outliers on the Petal Length and Sepal Width variables in the `iris` dataset.  We can clearly see an outlier which is far from the mass of the data (lower left): 

```r
ggplot(iris, aes(x=Sepal.Width, y=Petal.Length)) + 
  geom_point() +
  ggtitle("Scatterplot for Petal Length vs Sepal Width in iris dataset")
```

<img src="outliers_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Let's have a look at outliers on the Petal Length and Petal Width variables in the `iris` dataset by fitting a smooth model. Here the outliers are the points that do not fit the smooth model:

```r
ggplot(iris, aes(x=Petal.Width, y=Petal.Length)) + 
  geom_point() + 
  geom_smooth() + 
  geom_density2d(col="red",bins=4) + 
  ggtitle("Scatterplot for Petal Length vs Petal Width in iris dataset")
```

```
## `geom_smooth()` using method = 'loess' and formula 'y ~ x'
```

<img src="outliers_files/figure-html/unnamed-chunk-4-1.png" width="672" />

Lets have a look at outliers in the `diamond` dataset using a parallel coordinate plot. We can see an outlier on the carat, cut, color, and clarity variables that is not an outlier on individual variables:

```r
library(GGally)
ggparcoord(diamonds[1:1000,], columns=1:5, scale="uniminmax", alpha=0.8) + 
  ggtitle("Parallel coordinate plot of diamonds dataset")
```

<img src="outliers_files/figure-html/unnamed-chunk-5-1.png" width="672" />

### Categorical Outliers
Outliers can be rare on a categorical scale. Certain combinations of categories are rare or should not occur at all. Fluctuation diagrams can be used to find such outliers. We can see rare cases in the `HairEyeColor` dataset:

```r
library(datasets)
library(extracat)

fluctile(HairEyeColor)
```

<img src="outliers_files/figure-html/unnamed-chunk-6-1.png" width="768" />

## Handling Outliers

Identifying outliers using plots and fitting models is relatively easy compared to what to do after identifying the outliers. Outliers can be rare cases, unusual values, or genuine errors. Genuine errors must be corrected if possible or else they must be removed. Imputation of outliers is complicated and appropriate background knowledge is required. 

A strategy for dealing with outliers is as follows

1. Plot the one-dimensional distributions of the variables using boxplots. Examine any extreme outliers to see if they are rare values or errors and decide if they should be removed or imputed. 

2. For outliers which are extreme on one dimension, examine their values on other dimensions to decide whether they should be discarded or not. Discard values that are outliers on more than one dimension.

3. Consider cases which are outliers in a higher dimensions but not in lower dimensions. Decide whether they are errors or not and consider discarding or imputing the errors.

5. Plot boxplots and parallel coordinate plots by using grouping on a variable to find outliers in subsets of the data.

### Not informative

Consider the `diamonds` dataset. Let's have a look at the width (y) and depth (z) variables:

```r
ggplot(diamonds, aes(y=y)) + 
  geom_boxplot(color="black", fill="#9B3535") + 
  ggtitle("Ouliers on width variable in diamonds dataset")
```

<img src="outliers_files/figure-html/unnamed-chunk-7-1.png" width="672" />

```r
ggplot(diamonds, aes(y=z)) + 
  geom_boxplot(color="black", fill="#9B3535") + 
  ggtitle("Ouliers on depth variable in diamonds dataset")
```

<img src="outliers_files/figure-html/unnamed-chunk-8-1.png" width="672" />


```r
ggplot(diamonds, aes(y, z)) + 
  geom_point(col = "#9B3535") + 
  xlab("width") + 
  ylab("depth")
```

<img src="outliers_files/figure-html/unnamed-chunk-9-1.png" width="672" />

### More informative
The plots are not very informative due to the outliers. The same plots after filtering the outliers are much more informative:

```r
d2 <- filter(diamonds, y > 2 & y < 11 & z > 1 & z < 8)
ggplot(d2, aes(y=y)) + 
  geom_boxplot(color="black", fill="lightblue") + 
  ggtitle("Ouliers on width variable in diamonds dataset")
```

<img src="outliers_files/figure-html/unnamed-chunk-10-1.png" width="672" />

```r
d2 <- filter(diamonds, y > 2 & y < 11 & z > 1 & z < 8)
ggplot(d2, aes(y=z)) + 
  geom_boxplot(color="black", fill="lightblue") + 
  ggtitle("Ouliers on depth variable in diamonds dataset")
```

<img src="outliers_files/figure-html/unnamed-chunk-11-1.png" width="672" />

```r
d2 <- filter(diamonds, y > 2 & y < 11 & z > 1 & z < 8)
ggplot(d2, aes(y, z)) + 
  geom_point(shape = 21, color = "darkGrey", fill = "lightBlue", stroke = 0.1) + 
  xlab("width") + 
  ylab("depth")
```

<img src="outliers_files/figure-html/unnamed-chunk-12-1.png" width="672" />


## External Resources

- [Identify, describe, plot, and remove the outliers from the dataset](https://www.r-bloggers.com/identify-describe-plot-and-remove-the-outliers-from-the-dataset/){target="_blank"}: Plotting and removing outliers from a dataset

- [A Brief Overview of Outlier Detection Techniques](https://towardsdatascience.com/a-brief-overview-of-outlier-detection-techniques-1e0b2c19e561){target="_blank"}: Discussion of the theoretical aspect of outlier detection



