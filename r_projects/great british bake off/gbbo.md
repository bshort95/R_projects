---
title: "2.6"
author: "Brandon Short"
date: "January 17, 2022"
output:
  html_document:  
    keep_md: true
    toc: true
    toc_float: true
    code_folding: hide
    fig_height: 6
    fig_width: 12
    fig_align: 'center'
---





## importing datasets


```r
# Use this R-Chunk to import all your datasets!
only_first_last <- ratings %>% 
  group_by(series) %>% #changes the scope to look at the series instead of an index
  slice(1, n()) %>%  #indexes the rows according to their index location.
  mutate(which_episode = ifelse(episode == 1, "First", "Last")) %>% # changes the graph to show which episodes were the first and last 
  ungroup() %>% #ungroups the grouping
  mutate(series_f = as.factor(series)) # adding the mutated data to the graph

(only_first_last)
```

```
## # A tibble: 20 x 11
##    series episode uk_airdate viewers_7day viewers_28day network_rank
##    <fct>  <fct>   <date>            <dbl>         <dbl>        <int>
##  1 1      1       2010-08-17         2.24          7              NA
##  2 1      6       2010-09-21         2.75          1              NA
##  3 2      1       2011-08-16         3.1           2              NA
##  4 2      8       2011-10-04         5.06          1              NA
##  5 3      1       2012-08-14         3.85          1              NA
##  6 3      10      2012-10-16         6.74          1              NA
##  7 4      1       2013-08-20         6.6           1              11
##  8 4      10      2013-10-22         9.45          1               4
##  9 5      1       2014-08-06         8.51          1               1
## 10 5      10      2014-10-08        13.5           1               1
## 11 6      1       2015-08-05        11.6          11.7             1
## 12 6      10      2015-10-07        15.0          15.2             1
## 13 7      1       2016-08-24        13.6          13.9             1
## 14 7      10      2016-10-26        15.9          16.0             1
## 15 8      1       2017-08-29         9.46          9.72            1
## 16 8      10      2017-10-31        10.0          10.1             1
## 17 9      1       2018-08-28         9.55          9.92            1
## 18 9      10      2018-10-30        10.3          10.5             1
## 19 10     1       2019-08-27         9.62         10.0             1
## 20 10     10      2019-10-29        10.0          10.2             1
## # ... with 5 more variables: channels_rank <int>, bbc_iplayer_requests <dbl>,
## #   episode_count <dbl>, which_episode <chr>, series_f <fct>
```

## creating rating_10


```r
# Use this R-Chunk to clean & wrangle your data!
rating_10 <- ratings %>%
  group_by(series) %>%
  filter(10 %in% episode) %>%
  mutate(viewers_mean = mean(viewers_7day)) %>%
  ungroup() 
  
(rating_10)
```

```
## # A tibble: 80 x 10
##    series episode uk_airdate viewers_7day viewers_28day network_rank
##    <fct>  <fct>   <date>            <dbl>         <dbl>        <int>
##  1 3      1       2012-08-14         3.85             1           NA
##  2 3      2       2012-08-21         4.6              1           NA
##  3 3      3       2012-08-28         4.53             1           NA
##  4 3      4       2012-09-04         4.71             1           NA
##  5 3      5       2012-09-11         4.61             1           NA
##  6 3      6       2012-09-18         4.82             1           NA
##  7 3      7       2012-09-25         5.1              1           NA
##  8 3      8       2012-10-02         5.35             1           NA
##  9 3      9       2012-10-09         5.7              1           NA
## 10 3      10      2012-10-16         6.74             1           NA
## # ... with 70 more rows, and 4 more variables: channels_rank <int>,
## #   bbc_iplayer_requests <dbl>, episode_count <dbl>, viewers_mean <dbl>
```

## Data vis on network and channel rankings


```r
ggplot(data = only_first_last, aes(y = channels_rank))+
  geom_col(aes(x = series, fill = which_episode), width=.5, position = "dodge")
```

![](2_6_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
ggplot(data = only_first_last, aes(y = network_rank))+
  geom_col(aes(x = series, fill = which_episode), width=.5, position = "dodge")
```

![](2_6_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

## Observation for network and channel ranks

in the network ratings it is pretty consistent, as the ranks are number 1 every year since series 5. When it comes the   the channels ranking it fluctuates a little more with the overall ranking being better for the first episode opposed   to the last episode. this makes me think that people are more excited to watch the start of the show. this makes   sense logically, as a competition show it is easier for those who have watched through the entire show to be excited   about the final episode. considering that not everyone who starts the show will finish it, there is more of an   audience for the first eppisode.

## Data vis for people watching   


```r
grouped_rating<-(rating_10) %>%
  group_by(series) %>%
  slice(n())

ggplot(data = rating_10)+
  geom_point(aes(x = series, y = viewers_7day, color = episode))+
  geom_point (data = grouped_rating,aes(x = series, y = viewers_mean), shape =10, size = 6)
```

![](2_6_files/figure-html/unnamed-chunk-5-1.png)<!-- -->
  
## Observation from people watching  
  
As with most shows, there was a ramp up on viewership from season to season, but in this case there was a dropoff of   views after season 7. what is also interesting is that the most viewed episode looks to be the first one. which goes   in line with my observations from the last graph. after doing some reaseach it looks like the fan favorites were      series 5-7, and this is in part to the dynamics of contestants. 
