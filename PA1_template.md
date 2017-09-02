# Reproducible Research: Peer Assessment 1


```r
library(lubridate)
```

```
## Warning: package 'lubridate' was built under R version 3.3.3
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

## Loading and preprocessing the data

1. Load the data (i.e. read.csv())


```r
unzip("activity.zip")
activityData <- read.csv("activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
stepsPerDay <- aggregate(steps ~ date, activityData, sum)
```

## What is mean total number of steps taken per day?

1. Make a histogram of the total number of steps taken each day


```r
hist(stepsPerDay$steps, xlab="Steps", col="blue", main="Total steps per day", breaks=10)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->


2. Calculate and report the mean and median total number of steps taken per day


```r
mean(stepsPerDay$steps, na.rm = TRUE)
```

```
## [1] 10766.19
```


```r
median(stepsPerDay$steps, na.rm = TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
stepsInterval <- aggregate(steps ~ interval, activityData, mean)
plot(stepsInterval$interval, 
     stepsInterval$steps, 
     type="l", 
     ylab="Steps", 
     xlab="Interval (5 mins)", 
     main="Average steps accross a day in 5 minute intervals", 
     col="red")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
stepsInterval[which.max(stepsInterval$steps), 1]
```

```
## [1] 835
```

## Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nrow(activityData) - sum(complete.cases(activityData))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```Will use a strategy of setting NA values to the mean number of steps```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
meanSteps <- mean(activityData$steps, na.rm = TRUE)
activityData[is.na(activityData$steps), ]$steps <- meanSteps
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
stepsPerDayFilled <- aggregate(steps ~ date, activityData, sum)
hist(stepsPerDayFilled$steps, xlab="Steps", col="blue", main="Total steps per day", breaks=10)
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

```Setting the missing values to the mean value has resulted in the histogram being skewed and a larger difference between the mean number of steps and other buckets.```

## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activityData$date <- ymd(activityData$date)
activityData$weekDay <- ifelse(weekdays(activityData$date) %in% c("Saturday", "Sunday"), "Weekend", "Weekday")
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
stepsIntervalWeekdays <- aggregate(steps ~ interval, activityData[activityData$weekDay == "Weekday", ], mean)
stepsIntervalWeekends <- aggregate(steps ~ interval, activityData[activityData$weekDay == "Weekend", ], mean)

plot(stepsIntervalWeekdays$interval, 
     stepsIntervalWeekdays$steps, 
     type="l", 
     ylab="Steps", 
     xlab="Interval (5 mins)", 
     main="Average steps accross a day in 5 minute intervals (Weekdays)", 
     col="red")

lines(stepsIntervalWeekends$interval, stepsIntervalWeekends$steps, col="blue")

legend("top", legend=c("Weekdays", "Weekend"), col=c("red", "blue"), lty=1:2, cex=0.8)
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
