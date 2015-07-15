# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Assume activty.zip has been unzipped to activity.csv and exists in the working directory. Load the data into R via the read.csv command. 

```r
data <- read.csv("activity.csv")
```



## What is mean total number of steps taken per day?

Let's both look at the distribution of steps and calculate an overall mean and median

```r
library(ggplot2)
summed_steps<-aggregate(steps ~ date, data=data,sum)
ggplot(data=summed_steps,aes(x=steps))+geom_histogram()
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
mean(summed_steps$steps)
```

```
## [1] 10766.19
```

```r
median(summed_steps$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

Now we will take the mean steps for each interval and look at a typical day. After the plot we show the interval that contains the most steps in this average daily pattern. 

```r
typical_day <- aggregate(steps ~ interval, data=data,mean)
ggplot(data=typical_day,aes(x=interval,y=steps))+geom_line()
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
typical_day$interval[which.max(typical_day$steps)]
```

```
## [1] 835
```


## Imputing missing values
Now we explore missing values and the impact of analysis on replacing these missing values. 
First, the number of total number of missing values is 

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```
Let's replace those missing values with the number of steps from the corresponding interval of a typical day. We will save this as a new dataframe called mod_data and confirm that all NAs have been removed. 

```r
missing_intervals<-data$interval[is.na(data$steps)]
replacement_step <- numeric()
for(i in missing_intervals)
  {replacement_step <- c(replacement_step,typical_day$steps[typical_day$interval==i])}
mod_data<-data
mod_data$steps[is.na(mod_data$steps)]<-replacement_step
sum(is.na(mod_data$steps))
```

```
## [1] 0
```
Now lets revisit the histogram and mean/median from before:

```r
mod_summed_steps<-aggregate(steps ~ date, data=mod_data,sum)
ggplot(data=summed_steps,aes(x=steps))+geom_histogram()
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

```r
ggplot(data=mod_summed_steps,aes(x=steps))+geom_histogram()
```

![](PA1_template_files/figure-html/unnamed-chunk-6-2.png) 

```r
mean(summed_steps$steps)
```

```
## [1] 10766.19
```

```r
mean(mod_summed_steps$steps)
```

```
## [1] 10766.19
```

```r
median(summed_steps$steps)
```

```
## [1] 10765
```

```r
median(mod_summed_steps$steps)
```

```
## [1] 10766.19
```

It appears our efforts to replace missing values has no effect on the mean and a very small effect on the median. 

## Are there differences in activity patterns between weekdays and weekends?
