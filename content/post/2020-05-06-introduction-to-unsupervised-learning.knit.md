---
title: Introduction to Unsupervised Learning
author: Derek Sollberger
date: '2020-05-06'
slug: introduction-to-unsupervised-learning
categories:
  - machine learning
tags:
  - ggplot
header:
  caption: ''
  image: ''
  preview: yes
---

# Unsupervised Learning

* **Supervised learning** has the goal of making predictions with a set of *known* labels for the response variable.
* In **unsupervised learning**, we try to find structure in the data of the response variable without predetermined labels.

Goal: organize the items available in the `Animal Crossing` video game 

# Data set: Animal Crossing

Source:  VillagerDB, MetaCritic, and TidyTuesday

* [Animal Crossing Tidy Tuesday](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-05-05/readme.md)


```r
library("ggrepel")
library("tidyverse")

# critic <- readr::read_tsv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/critic.tsv')
# user_reviews <- readr::read_tsv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/user_reviews.tsv')
items <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/items.csv')
# villagers <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/villagers.csv')
```

# Exploratory Data Analysis

## variables


```r
dim(items)
```

```
## [1] 4565   16
```

```r
str(items)
```

```
## tibble [4,565 x 16] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ num_id       : num [1:4565] 12 14 17 19 20 21 23 24 26 26 ...
##  $ id           : chr [1:4565] "3d-glasses" "a-tee" "abstract-wall" "academy-uniform" ...
##  $ name         : chr [1:4565] "3D Glasses" "A Tee" "Abstract Wall" "Academy Uniform" ...
##  $ category     : chr [1:4565] "Accessories" "Tops" "Wallpaper" "Dresses" ...
##  $ orderable    : logi [1:4565] NA NA TRUE NA FALSE TRUE ...
##  $ sell_value   : num [1:4565] 122 140 390 520 2000 375 420 330 8990 8990 ...
##  $ sell_currency: chr [1:4565] "bells" "bells" "bells" "bells" ...
##  $ buy_value    : num [1:4565] 490 560 1560 2080 NA ...
##  $ buy_currency : chr [1:4565] "bells" "bells" "bells" "bells" ...
##  $ sources      : chr [1:4565] NA NA "Nook's Cranny" NA ...
##  $ customizable : logi [1:4565] NA NA NA NA NA FALSE ...
##  $ recipe       : num [1:4565] NA NA NA NA NA NA NA NA NA NA ...
##  $ recipe_id    : chr [1:4565] NA NA NA NA ...
##  $ games_id     : chr [1:4565] "nh" "nh" "nh" "nh" ...
##  $ id_full      : chr [1:4565] "item-3d-glasses" "item-a-tee" NA "item-academy-uniform" ...
##  $ image_url    : chr [1:4565] "https://villagerdb.com/images/items/full/3d-glasses.b8e1b5a.png" "https://villagerdb.com/images/items/full/a-tee.a1af443.png" NA "https://villagerdb.com/images/items/full/academy-uniform.4475eb5.png" ...
##  - attr(*, "problems")= tibble [2 x 5] (S3: tbl_df/tbl/data.frame)
##   ..$ row     : int [1:2] 4472 4473
##   ..$ col     : chr [1:2] "customizable" "customizable"
##   ..$ expected: chr [1:2] "1/0/T/F/TRUE/FALSE" "1/0/T/F/TRUE/FALSE"
##   ..$ actual  : chr [1:2] "Yes" "Yes"
##   ..$ file    : chr [1:2] "'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/items.csv'" "'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-05-05/items.csv'"
##  - attr(*, "spec")=
##   .. cols(
##   ..   num_id = col_double(),
##   ..   id = col_character(),
##   ..   name = col_character(),
##   ..   category = col_character(),
##   ..   orderable = col_logical(),
##   ..   sell_value = col_double(),
##   ..   sell_currency = col_character(),
##   ..   buy_value = col_double(),
##   ..   buy_currency = col_character(),
##   ..   sources = col_character(),
##   ..   customizable = col_logical(),
##   ..   recipe = col_double(),
##   ..   recipe_id = col_character(),
##   ..   games_id = col_character(),
##   ..   id_full = col_character(),
##   ..   image_url = col_character()
##   .. )
```


```r
table(items$category)
```

```
## 
## Accessories     Bottoms        Bugs     Dresses        Fish    Flooring 
##         123         142          80         239         101         324 
##     Flowers     Fossils       Fruit   Furniture        Hats       Music 
##         109          76          12        1264         230          95 
##      Photos   Seashells       Shoes       Socks       Tools        Tops 
##         840           8          89          60          74         289 
##   Umbrellas     Usables   Wallpaper 
##          67          88         255
```

```r
summary(items$buy_value)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##      40     870    1300    6960    2700 1200000    1014
```

```r
# table(items$buy_currency)
summary(items$sell_value)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##       5     240     390    2261    1000  300000      36
```

```r
summary(items$num_id)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      12    1722    3569    3662    5607    7443
```

```r
summary(items$recipe)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##   1.000   2.000   3.000   4.711   6.000  90.000    3977
```

## numerical variables


```r
items %>%
  ggplot(aes(x = buy_value, y = sell_value, color = category)) +
  geom_point() +
  coord_trans(x = "log10") + 
  labs(title = "Items in Animal Crossing",
       subtitle = "Sell Values vs Buy Values",
       caption = "Math 32") +
  scale_x_continuous(name = "Buy Value",
                     breaks = c(0, 10, 100, 1000, 10000, 100000, 1000000),
                     labels = c("0", "10", "100", "1000", "10000", "100000", "1000000")) +
  scale_y_continuous(name = "Sell Value",
                     breaks = c(0, 50000, 100000, 150000, 200000, 250000, 300000),
                     labels = c("0", "50000", "100000", "150000", "200000", "250000", "300000")) +
  theme_minimal()
```

```
## Warning: Removed 1035 rows containing missing values (geom_point).
```

<img src="2020-05-06-introduction-to-unsupervised-learning_files/figure-html/unnamed-chunk-5-1.png" width="672" />


# K-Means Clustering

## first look


```r
df_numerical <- items %>%
  select(num_id, buy_value, sell_value, recipe) %>%
  filter(complete.cases(.))

summary(df_numerical$buy_value)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     240    3780    7080   16912   16480  320000
```

```r
summary(df_numerical$sell_value)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##      60     945    1770    4228    4120   80000
```


```r
clusters4 <- kmeans(df_numerical, 4)

items_with_clusters <- cbind(df_numerical, clusters4$cluster)
colnames(items_with_clusters) <- c("ID", "buy", "sell", "recipe", "cluster")
items_with_clusters$cluster <- factor(items_with_clusters$cluster)
```


```r
x_breaks <- c(0, 10, 100, 1000, 10000, 100000, 1000000)
y_breaks <- c(0, 10000, 20000, 30000, 40000, 50000, 60000, 70000, 80000)

items_with_clusters %>%
  ggplot() +
  geom_point(aes(x = buy, y = sell)) +
  geom_point(aes(x = buy_value, y = sell_value), 
             data = data.frame(clusters4$centers),
             size = 5,
             color = "blue") +
  coord_trans(x = "log10") +
  labs(title = "Items in Animal Crossing",
       subtitle = "Cluster Centers",
       caption = "Math 32") +
  scale_x_continuous(name = "Buy Value",
                     breaks = x_breaks,
                     labels = paste(x_breaks)) +
  scale_y_continuous(name = "Sell Value",
                     breaks = y_breaks,
                     labels = paste(y_breaks)) +
  theme_minimal()
```

<img src="2020-05-06-introduction-to-unsupervised-learning_files/figure-html/unnamed-chunk-8-1.png" width="672" />


```r
items_with_clusters %>%
  ggplot(group = cluster) +
  geom_point(aes(x = buy, y = sell, color = cluster), size = 3) +
  coord_trans(x = "log10") +
  labs(title = "Items in Animal Crossing",
       subtitle = "4 Clusters",
       caption = "Math 32") +
  scale_x_continuous(name = "Buy Value",
                     breaks = x_breaks,
                     labels = paste(x_breaks)) +
  scale_y_continuous(name = "Sell Value",
                     breaks = y_breaks,
                     labels = paste(y_breaks)) +
  theme_minimal()
```

<img src="2020-05-06-introduction-to-unsupervised-learning_files/figure-html/unnamed-chunk-9-1.png" width="672" />


## selecting the amount of clusters

Selecting the number of clusters is rather subjective.

* too few clusters: items that are "far" from each other are still grouped together
* too many clusters: harder to describe and label groups later

We start with an $d$-dimensional data set of numerical variables and prescribe a number $k$ for the number of clusters and run the `kmeans` algorithm.

* Each cluster $C_{k}$ has $n_{k}$ points labeled $x_{i}$ in $d$-dimensional space
* Each cluster has a cluster center $\mu_{k}$
* Each cluster has a *within-sum-of-squares*

$$WSS = \displaystyle\sum_{x_{i} \in C_{k}} (x_{i} - \mu_{k})^{2}$$
Thus, our metric for the clustering will be the reported *total-within-sum-of-squares*

$$TWSS = \displaystyle\sum_{j=1}^{k}\sum_{x_{i} \in C_{k}} (x_{i} - \mu_{k})^{2}$$
For example, the total-within-sum-of-squares for the 4-cluster results above is 3.1486354\times 10^{10}.


## scree plot

It is common practice to build a *scree plot* and look for the "elbow".


```r
clusters1 <- kmeans(df_numerical, 1)
clusters2 <- kmeans(df_numerical, 2)
clusters3 <- kmeans(df_numerical, 3)
clusters4 <- kmeans(df_numerical, 4)
clusters5 <- kmeans(df_numerical, 5)
clusters6 <- kmeans(df_numerical, 6)
clusters7 <- kmeans(df_numerical, 7)
clusters8 <- kmeans(df_numerical, 8)
clusters9 <- kmeans(df_numerical, 9)

TSS_vector <- c(clusters1$tot.withinss, clusters2$tot.withinss, clusters3$tot.withinss, 
                clusters4$tot.withinss, clusters5$tot.withinss, clusters6$tot.withinss, 
                clusters7$tot.withinss, clusters8$tot.withinss, clusters9$tot.withinss)

k_vals <- 1:9
df_for_scree_plot <- data.frame(k_vals, TSS_vector)
```


```r
df_for_scree_plot %>%
  ggplot(aes(x = k_vals, y = TSS_vector)) +
  geom_point(size = 3) +
  geom_line() +
  labs(title = "Scree Plot",
       subtitle = "(find the 'elbow')",
       caption = "Math 32",
       x = "number of clusters",
       y = "total-within-sum-of-squares") +
  scale_x_discrete(name = "number of clusters",
                   breaks = paste(1:9),
                   labels = paste(1:9),
                   limits = 1:9) +
  theme_minimal()
```

<img src="2020-05-06-introduction-to-unsupervised-learning_files/figure-html/unnamed-chunk-11-1.png" width="672" />

# Conclusion

It appears that the optimal number of clusters for our data set is $k = 3$.


```r
items_with_clusters <- cbind(df_numerical, clusters3$cluster)
colnames(items_with_clusters) <- c("ID", "buy", "sell", "recipe", "cluster")
items_with_clusters$cluster <- factor(items_with_clusters$cluster)

df_categorical <- items %>%
  select(num_id, name, category) %>%
  filter(complete.cases(.))
colnames(df_categorical) <- c("ID", "name", "category")

main_df <- df_categorical %>%
  inner_join(items_with_clusters, by = "ID")
```


```r
main_df %>%
  ggplot(group = cluster) +
  geom_point(aes(x = buy, y = sell, color = cluster), size = 3) +
  coord_trans(x = "log10") +
  labs(title = "Items in Animal Crossing",
       subtitle = "Optimal Clustering",
       caption = "Math 32") +
  scale_x_continuous(name = "Buy Value",
                     breaks = x_breaks,
                     labels = paste(x_breaks)) +
  scale_y_continuous(name = "Sell Value",
                     breaks = y_breaks,
                     labels = paste(y_breaks)) +
  theme_minimal()
```

<img src="2020-05-06-introduction-to-unsupervised-learning_files/figure-html/unnamed-chunk-13-1.png" width="672" />

# Appendix: label a sample of points from each cluster


```r
label_df <- main_df %>%
  group_by(cluster) %>%
  sample_n(2)

main_df %>%
  ggplot(group = cluster) +
  geom_point(aes(x = buy, y = sell, color = cluster), size = 3) +
  geom_label_repel(aes(x = buy, y = sell, label = name), 
                   data = label_df,
                   nudge_x = -1000,
                   nudge_y = 10000) +
  coord_trans(x = "log10") +
  labs(title = "Items in Animal Crossing",
       subtitle = "Optimal Clustering",
       caption = "Math 32") +
  scale_x_continuous(name = "Buy Value",
                     breaks = x_breaks,
                     labels = paste(x_breaks)) +
  scale_y_continuous(name = "Sell Value",
                     breaks = y_breaks,
                     labels = paste(y_breaks)) +
  theme_minimal()
```

<img src="2020-05-06-introduction-to-unsupervised-learning_files/figure-html/unnamed-chunk-14-1.png" width="672" />
