# Reproducible Research Course Project 1
Carlos Castro  
6 de agosto de 2017  


Reproducible Research Course Project 1
======================================

Author: Carlos Castro
date: "6 de agosto de 2017"

Loading and processing the data
-------------------------------
* Load the data

```r
activity <- read.csv("./data1/activity.csv")
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```
* Processing the data (convert the variable date from factor to date)

```r
activity$date <- as.Date(activity$date, "%Y-%m-%d")
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Date, format: "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```
What is mean total number of steps taken per day?
-------------------------------------------------
* Histogram of Aggregate Steps by Day

```r
TotalStepsByDay <-  aggregate(steps ~ date, activity, sum)
with(TotalStepsByDay, hist(steps, breaks=5, col= "cadetblue3",main = "Total Steps Per DAy", xlab = "# Steps"))
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

* Mean and median number of steps taken each day

```r
MedianSteps <- median(TotalStepsByDay$steps)
MedianSteps
```

```
## [1] 10765
```

```r
AvgSteps <- mean(TotalStepsByDay $steps)
AvgSteps
```

```
## [1] 10766.19
```
What is the average daily activity pattern?
-------------------------------------------
* Time series plot of the average number of steps taken

```r
AvgStepsbyInterval <- aggregate(steps ~ interval, activity, mean,na.rm=TRUE)
with(AvgStepsbyInterval, plot(steps,type = "l", main = "Average Steps Per Interval", xlab = "Interval", ylab= "Average steps"))
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

*The 5-minute interval that, on average, contains the maximum number of steps

```r
IntevalMAxSteps <- AvgStepsbyInterval[which.max(AvgStepsbyInterval$steps),]
IntevalMAxSteps
```

```
##     interval    steps
## 104      835 206.1698
```
Code to describe and show a strategy for imputing missing data
--------------------------------------------------------------
* Number of Missing Values

```r
MissingData<-is.na(activity$steps)
sum(MissingData)
```

```
## [1] 2304
```
* Strategy for filling in all of the missing values in the dataset

Here the strategy is that all steps with NA are replaced with the average of all data that are not NA's. The missing values now are zero (0)


```r
Fullactivity <-activity
Fullactivity$steps[which(is.na(Fullactivity$steps))] <- mean(Fullactivity$steps, na.rm=TRUE)
MissingData2<-is.na(Fullactivity$steps)
sum(MissingData2)
```

```
## [1] 0
```

* Histogram of the total number of steps taken each day after missing values are imputed

```r
FullTotalStepsByDay <-  aggregate(steps ~ date, Fullactivity, sum)
with(FullTotalStepsByDay, hist(steps, breaks=5, col= "cyan4",main = "Total Steps Per DAy (Update)", xlab = "# Steps"))
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
MedianSteps2 <- median(FullTotalStepsByDay$steps)
MedianSteps2
```

```
## [1] 10766.19
```

```r
AvgSteps2 <- mean(FullTotalStepsByDay$steps)
AvgSteps2
```

```
## [1] 10766.19
```

Accoding with the results, the median value shows a little difference (10765 vs 10766.19), while the mean value is the samein both scenarios (10766.19). So, the impact of the strategy used is almost null.

Are there differences in activity patterns between weekdays and weekends?
--------------------------------------------------------------------------

* Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
Fullactivity$day <- ifelse(as.POSIXlt(Fullactivity$date)$wday %in% c(0,6), "weekend", "weekday")
```

* Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends




```r
library(ggplot2)
AveragedFullactivity <- aggregate(steps ~ interval + day, data=Fullactivity, mean)
qplot(interval, steps, data = AveragedFullactivity, facets= day ~ ., geom = c("line"), main = "Average Steps Weekends vs Weekdays")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->




