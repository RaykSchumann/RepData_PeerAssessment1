---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

The variables included in this dataset are:
• steps: Number of steps taking in a 5-minute interval (missing values are
coded as NA)
• date: The date on which the measurement was taken in YYYY-MM-DD
format
• interval: Identifier for the 5-minute interval in which measurement was
taken


1. Init & Load the data


```r
library(ggplot2) 
```

```
## Warning: package 'ggplot2' was built under R version 3.5.3
```

```r
activity <- read.csv("~/RStuff/DataScienceCourse/05 Reproducible Research/04 Program/data/activity.csv")
```


2. Process/transform the data (if necessary) into a format suitable for your
analysis


```r
totalSteps<-aggregate(steps~date,data=activity,sum,na.rm=TRUE)
MeanStepsPerInterval<-aggregate(steps~interval,data=activity,mean,na.rm=TRUE)
```



## What is mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
hist(totalSteps$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->



2. Calculate and report the mean and median total number of steps taken
per day


```r
mean(totalSteps$steps)
```

```
## [1] 10766.19
```

```r
median(totalSteps$steps)
```

```
## [1] 10765
```

* The **mean** total number of steps taken per day is 
    1.0766189\times 10^{4} steps.
* The **median** total number of steps taken per day is 
    10765 steps.


## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis)
and the average number of steps taken, averaged across all days (y-axis)


```r
stepsInterval<-aggregate(steps~interval,data=activity,mean,na.rm=TRUE)
plot(steps~interval,data=stepsInterval,type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->



2. Which 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps?


```r
stepsInterval[which.max(stepsInterval$steps),]$interval
```

```
## [1] 835
```

It is the **835th** interval.



## Inputing missing values

* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```
Total 2304 rows are missing.

* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

: I used a strategy for filing in all of the missing values with the mean for that 5-minute interval. Therefore I created a new column steps_mod


```r
activity$steps_mod=ifelse(is.na(activity$steps),
                          MeanStepsPerInterval[match(activity$interval,MeanStepsPerInterval$interval),'steps']
                         ,activity$steps
                         ) 
```



* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 

```r
totalSteps2<-aggregate(steps_mod~date,data=activity,sum)
hist(totalSteps2$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
mean(totalSteps2$steps)
```

```
## [1] 10766.19
```

```r
median(totalSteps2$steps)
```

```
## [1] 10766.19
```
* The **mean** total number of steps taken per day is 
1.0766189\times 10^{4} steps.
* The **median** total number of steps taken per day is 
1.0766189\times 10^{4} steps.

* Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

: The **mean** value is the **same** as the value before imputing missing data because we put the mean value for that particular 5-min interval. The median value shows **a little** difference : but it depends on **where the missing values are**.

Are there differences in activity patterns between weekdays and weekends?
---------------------------------------------------------------------------

* Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
activity$day=ifelse(as.POSIXlt(as.Date(activity$date))$wday%%6==0,
                          "weekend","weekday")
# For Sunday and Saturday : weekend, Other days : weekday 
activity$day=factor(activity$day,levels=c("weekday","weekend"))
```


* Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 


```r
stepsInterval2=aggregate(steps_mod~interval+day,activity,mean)


ggplot(stepsInterval2, aes(x=interval, y=steps_mod, group=day)) +
  geom_line(aes(color=day))
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
