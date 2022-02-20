---
title: "Brandon Short"
author: "YOUR NAME"
date: "January 28, 2022"
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






```r
# Use this R-Chunk to import all your datasets!
camp <- read.csv('https://raw.githubusercontent.com/byui-cse/cse450-course/master/data/bank.csv')
```

## Background

_Place Task Background Here_

## Data Wrangling

```r
view(camp)
```



```r
sumcamp <-camp%>%
  summarise(job,education,age,day_of_week,y)

  
camyes <-sumcamp%>%
  filter(y == 'yes')
  

aq1 = sumcamp%>%
  summarise(job,y)%>%
  group_by(job,y)%>%
  mutate(count_y = n()/nrow(camyes))%>%
  mutate(total = n()/nrow(sumcamp))%>%
  mutate(count_y = replace(count_y, y == "no", total))%>%
  ungroup%>%
  mutate(y = replace(y, y == "no", "total"))


bq1 = sumcamp%>%
  summarise(age,y)%>%
  group_by(age,y)%>%
  mutate(count_y = n()/nrow(camyes))%>%
  mutate(total = n()/nrow(sumcamp))%>%
  mutate(count_y = replace(count_y, y == "no", total))%>%
  ungroup%>%
  mutate(y = replace(y, y == "no", "total"))



cq1 = sumcamp%>%
  summarise(education,y)%>%
  group_by(education,y)%>%
  mutate(count_y = n()/nrow(camyes))%>%
  mutate(total = n()/nrow(sumcamp))%>%
  mutate(count_y = replace(count_y, y == "no", total))%>%
  ungroup%>%
  mutate(y = replace(y, y == "no", "total"))
```





```r
# Use this R-Chunk to clean & wrangle your data!

ggplot(data = aq1)+
  geom_col(aes(x = job, y = count_y, fill = y), position = "dodge")+
  labs(y = "percentage of total count")+
  theme_minimal_grid()+
  scale_fill_manual(values = c( "#FF0000",
                                "#0000FF"))
```

![](test_files/figure-html/tidy_data-1.png)<!-- -->

```r
ggplot(data = bq1)+
  geom_col(aes(x = age, y = count_y, fill = y), position = "dodge")+
  labs(y = "percentage of total count")+
  theme_bw()+
  scale_fill_manual(values = c( "#FF0000",
                                "#0000FF"))
```

![](test_files/figure-html/tidy_data-2.png)<!-- -->

```r
ggplot(data = cq1)+
  geom_col(aes(x = education, y = count_y, fill = y), position = "dodge")+
  labs(y = "percentage of total count")+
  theme_test()+
  scale_fill_manual(values = c( "#FF0000",
                                "#0000FF"))
```

![](test_files/figure-html/tidy_data-3.png)<!-- -->

## Data Visualization


```r
# Use this R-Chunk to plot & visualize your data!
#effect of days

q2 <- camyes%>%
  summarise(day_of_week)%>%
  group_by(day_of_week)%>%
  mutate(count = n())%>%
  ungroup()%>%
  mutate(day_of_week = factor(day_of_week, levels=c("mon", 
                                      "tue", 
                                      "wed", 
                                      "thu", 
                                      "fri")))
```


```r
view(q2)
```



```r
ggplot(data = q2)+
  geom_point(aes(x = day_of_week, y = count))+
  geom_line(aes(x = day_of_week, y = count,group = 1))+
  scale_y_continuous(limits = c(500,1500))+
  labs(x = "day of week", y = "number of yes responses")
```

![](test_files/figure-html/unnamed-chunk-6-1.png)<!-- -->
## Conclusions

```r
view(camyes)
```



```r
aq3 = camyes%>%
  summarise(job,y)%>%
  group_by(job,y)%>%
  mutate(count = n())%>%
  ungroup()%>%
  group_by(count)%>%
  arrange(desc(count))%>%
  slice(1)%>%
  ungroup()%>%
  arrange(desc(count))%>%
  slice(0:3)

bq3 = camyes%>%
  summarise(age,y)%>%
  group_by(age,y)%>%
  mutate(count = n())%>%
  ungroup()%>%
  group_by(count)%>%
  arrange(desc(count))%>%
  slice(1)%>%
  ungroup()%>%
  arrange(desc(count))%>%
  slice(0:10)

cq3 = camyes%>%
  summarise(education,y)%>%
  group_by(education,y)%>%
  mutate(count = n())%>%
  ungroup()%>%
  group_by(count)%>%
  arrange(desc(count))%>%
  slice(1)%>%
  ungroup()%>%
  arrange(desc(count))%>%
  slice(0:3)
```



```r
view(aq3)
```


```r
gaq3 <- ggplot(data = aq3) + 
          geom_col(aes(x = job, y = count, fill = job))+
          labs(y = "")+
          theme(legend.position = "hidden")          
  
gbq3 <- ggplot(data = bq3) + 
          geom_col(aes(x = age, y = count, fill = age))+
          scale_x_continuous(breaks = seq(25,40))+
          labs(y = "")+
          theme(legend.position = "hidden")

gcq3 <- ggplot(data = cq3) + 
          geom_col(aes(x = education, y = count, fill = education))+
          labs(y = "")+
          theme(legend.position = "hidden")

plot_grid(gaq3, gbq3, gcq3, label_size = 12, rows = 3)
```

![](test_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
