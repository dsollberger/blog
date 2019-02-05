---
title: Introduction to R Workshop
author: Derek Sollberger
date: '2019-02-05'
slug: introduction-to-r-workshop
categories:
  - R
tags:
  - ggplot
  - gganimate
  - ESL
  - workshop
header:
  caption: ''
  image: ''
  preview: yes
---

# Packages


```r
workshop_packages <-c("ggplot2", "mosaic", "gganimate")
install.packages(workshop_packages)
```


```r
library("gganimate")
```

```
## Loading required package: ggplot2
```

```r
library("ggplot2")
library("mosaic")
```

```
## Loading required package: dplyr
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```
## Loading required package: lattice
```

```
## Loading required package: ggformula
```

```
## Loading required package: ggstance
```

```
## 
## Attaching package: 'ggstance'
```

```
## The following objects are masked from 'package:ggplot2':
## 
##     geom_errorbarh, GeomErrorbarh
```

```
## 
## New to ggformula?  Try the tutorials: 
## 	learnr::run_tutorial("introduction", package = "ggformula")
## 	learnr::run_tutorial("refining", package = "ggformula")
```

```
## Loading required package: mosaicData
```

```
## Loading required package: Matrix
```

```
## 
## The 'mosaic' package masks several functions from core packages in order to add 
## additional features.  The original behavior of these functions should not be affected by this.
## 
## Note: If you use the Matrix package, be sure to load it BEFORE loading mosaic.
```

```
## 
## Attaching package: 'mosaic'
```

```
## The following object is masked from 'package:Matrix':
## 
##     mean
```

```
## The following objects are masked from 'package:dplyr':
## 
##     count, do, tally
```

```
## The following object is masked from 'package:ggplot2':
## 
##     stat
```

```
## The following objects are masked from 'package:stats':
## 
##     binom.test, cor, cor.test, cov, fivenum, IQR, median,
##     prop.test, quantile, sd, t.test, var
```

```
## The following objects are masked from 'package:base':
## 
##     max, mean, min, prod, range, sample, sum
```

# Data


```r
head(Weather)
```

```
## # A tibble: 6 x 25
## # Groups:   city, year [1]
##   city  date        year month   day high_temp avg_temp low_temp high_dewpt
##   <chr> <date>     <dbl> <int> <dbl>     <dbl>    <dbl>    <dbl>      <dbl>
## 1 Auck~ 2016-01-01  2016     1     1        68       65       62         64
## 2 Auck~ 2016-01-02  2016     1     2        68       66       64         64
## 3 Auck~ 2016-01-03  2016     1     3        77       72       66         70
## 4 Auck~ 2016-01-04  2016     1     4        73       66       60         66
## 5 Auck~ 2016-01-05  2016     1     5        69       62       55         55
## 6 Auck~ 2016-01-06  2016     1     6        69       63       57         54
## # ... with 16 more variables: avg_dewpt <dbl>, low_dewpt <dbl>,
## #   high_humidity <dbl>, avg_humidity <dbl>, low_humidity <dbl>,
## #   high_hg <dbl>, avg_hg <dbl>, low_hg <dbl>, high_vis <dbl>,
## #   avg_vis <dbl>, low_vis <dbl>, high_wind <dbl>, avg_wind <dbl>,
## #   low_wind <dbl>, precip <chr>, events <chr>
```

```r
table(Weather$city)
```

```
## 
##  Auckland   Beijing   Chicago    Mumbai San Diego 
##       731       731       731       731       731
```

```r
table(Weather$year)
```

```
## 
## 2016 2017 
## 1830 1825
```

# Sample Statistics


```r
mean(Weather$high_temp)
```

```
## [1] 71.32421
```

```r
median(Weather$precip)
```

```
## [1] "0"
```

```r
sd(Weather$avg_wind)
```

```
## [1] 3.865026
```

# Plotting


```r
Chicago <- subset(Weather, city == "Chicago")

# boxplots
ggplot(Chicago, aes(x = month, y = high_temp, group = month, fill = month)) +
  geom_boxplot() +
  labs(title = "Chicago Weather (2016-2017)",
       x = "month",
       y = "high temperature")
```

<img src="2019-02-05-introduction-to-r-workshop_files/figure-html/unnamed-chunk-5-1.png" width="672" />

# Regression


```r
ggplot(Chicago, aes(x = high_temp, y = low_temp, color = year)) +
  geom_point() +
  geom_smooth(method = "lm") +
  labs(title = "Chicago Weather (2016-2017)",
       x = "high temperature",
       y = "low temperature") +
  theme_minimal()
```

<img src="2019-02-05-introduction-to-r-workshop_files/figure-html/unnamed-chunk-6-1.png" width="672" />

# Hypothesis Testing


```r
SanDiego <- subset(Weather, city == "San Diego")

# Do Chicago and San Diego have the same high temperatures?
t.test(Chicago$high_temp, SanDiego$high_temp, alternative = "two.sided")
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  Chicago$high_temp and SanDiego$high_temp
## t = -14.221, df = 886.31, p-value < 2.2e-16
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -13.21560 -10.01012
## sample estimates:
## mean of x mean of y 
##  61.00821  72.62107
```

# Animation


```r
ggplot(Weather, aes(x = high_temp, y = low_temp, color = city)) +
  geom_point() +
  labs(title = "Worldwide Weather (2016-2017)",
       caption = "Source: Mosaic Foundation",
       subtitle = "(by months)",
       x = "high temperature",
       y = "low temperature") +
  theme_minimal() +
  transition_states(month, transition_length = 1, state_length = 1)
```

![](2019-02-05-introduction-to-r-workshop_files/figure-html/unnamed-chunk-8-1.gif)<!-- -->

