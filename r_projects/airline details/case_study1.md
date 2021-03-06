---
title: "Case Study 1: Give your Visualization Wings to Fly"
author: "Brandon Short"
date: "January 27, 2022"
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
img <- readJPEG('blue-clouds.jpeg')
library(nycflights13)
```

## Background

You just started your internship at a big firm in New York, and your manager gave you an extensive file of flights that departed JFK, LGA, or EWR in 2013. From this data your manager wants you to answer the following questions:


```r
#If I am leaving before noon, which two airlines do you recommend at each airport (JFK, LGA, EWR) that will have the lowest delay time at the 75th percentile?#

q1 <- flights%>% #q1 represents question 1
  filter(sched_dep_time < 1200)%>% # all planes scheduled to leave before noon
  filter(origin == 'JFK'| origin ==  'LGA'|origin ==  'EWR')%>% # the three airports we are focusing on
  summarise(origin, carrier, delay = arr_delay + dep_delay) # grabbing the needed data
```


```r
# Which origin airport is best to minimize my chances of a late arrival when I am using Delta Airlines?

q2 <-flights%>%
  filter(carrier == 'DL')%>% ## airports flying delta
  group_by(origin)%>% ## group by airports
  mutate(avg_late_arr = mean(arr_delay, na.rm = T))%>% ## create a column with the avarage arrival delay
  ungroup()%>% 
  summarise(origin,avg_late_arr) # grabbing the needed data
```


```r
#Which destination airport is the worst airport for arrival time?”
q3 <-flights%>%
  summarise(dest,arr_delay,month) # grabbing the needed data, not much wrangling was needed.

q3p <-q3%>%
  filter(arr_delay > 500) %>%
  group_by(dest)%>%
  mutate(count = n())
```

# Question 1
### If I am leaving before noon, which two airlines do you recommend at each airport (JFK, LGA, EWR) that will have the lowest delay time at the 75th percentile?


```r
ggplot(data = q1)+
  theme_dark()+
  geom_boxplot(aes( y = delay, fill = carrier))+
  facet_grid(~origin, drop = TRUE)+
  scale_y_continuous(breaks = seq(0, 100, by = 20),limits = c(0, 100),)+
  theme(axis.ticks.x = element_blank(),
  axis.text.x = element_blank())+
  labs(title = "carriers per airport according to their delay time")
```

![](case_study1_files/figure-html/unnamed-chunk-5-1.png)<!-- -->
  
### Results  

the two best flights according to least delay time per airport are as follows

EWR: Endeavor Air and Delta.    
JFK: Delta and Hawaiian Airlines.    
LGA: Delta and Frontier Airlines.  
  
I used a box-plot to convey this data because it does the best job in showing the different percentiles,  
for example if the boss than had a question about the mean or the overall statistics of the flights  
this would be also able to answer those question. 

# Question 2
### Which origin airport is best to minimize my chances of a late arrival when I am using Delta Airlines?

```r
ggplot(data = q2)+
  background_image(img) +
  geom_col(aes(x = origin, y = avg_late_arr, color = origin))+
  theme(legend.position = "hidden")+
  labs(y = "average late arrival time (minutes)",
       x = "source airport",
       title = "source airports late arrival time flying Delta.")
```

![](case_study1_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

### Results  

 the airport with the least average delay time flying with delta is JFK, to the point where the lack of delay is   substantial, and it lies in the negative numbers. this means that you have a higher chance of arriving early.  
   
 I chose to use a bar chart because it really displays the vast differences in the times.The cloudy background   adds a little interest.
 
# Question 3
### Which destination airport is the worst airport for arrival time?”

```r
# Use this R-Chunk to plot & visualize your data!
ggplot(data = q3)+
  geom_hline( yintercept = 500)+
  geom_point(data = q3p, aes(x = month, y = arr_delay, color = dest, size = count))+
  theme(legend.position = "hidden")+
  geom_point(aes(x = month, y = arr_delay, color = dest))+
  scale_x_continuous(breaks = seq(1,12))+
  theme(legend.position = "bottom")+
  guides(colour = guide_legend(nrow = 7, override.aes = list(size = 4)))+
  labs(y = "arrival delay(minutes)",
       legend = "destination airport",
       title = "finding the worst airport for arrival time")
```

![](case_study1_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

### Results  


although many flights had their bad days, the most consistently worst destination airport when it comes to arrival time is ATL. as he showed up the most over the threshold that was set at 500 minutes.
the size of each point over the threshold represents the amount of times that airport has breached that threshold. and ATL has most consistently have broken that threshold.

this was the toughest data to graph because I wanted to include all the flights, but i didn't want to distract from the purpose of the graph. overall the top portion is clearly distinguished from rest which is what i wanted to accomplish.
