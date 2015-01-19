# Reproducible Research: Peer Assessment 1
Daniel Santana  


## Loading and preprocessing the data

```r
unzip('activity.zip')
actdata <- read.csv('activity.csv')
```

## What is mean total number of steps taken per day?
* Make a histogram of the total number of steps taken each day


```r
steps_per_day <- aggregate(steps ~ date, data = actdata, FUN = sum)
hist(steps_per_day$steps, main='Steps per day', xlab = 'steps', ylab = 'days')
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

OR


```r
barplot(steps_per_day$steps, names.arg = steps_per_day$date, xlab = 'days', ylab = 'steps')
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

2. Calculate and report the **mean** and **median** total number of
   steps taken per day


**mean**   = 1.0766189\times 10^{4} steps.

```r
mean(steps_per_day$steps)
```

```
## [1] 10766.19
```

**median** = 10765 steps.

```r
median(steps_per_day$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
* Make a time series plot (i.e. type = 'l') of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps_interval <- aggregate(steps ~ interval, data = actdata, mean, na.rm = T)
plot(steps ~ interval, data = steps_interval, type = 'l')
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

* Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps? 

```r
steps_interval[which.max(steps_interval$steps), ]$interval
```

```
## [1] 835
```

It is the interval number **835**.

## Imputing missing values
* Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(actdata$steps))
```

```
## [1] 2304
```
Total 2304 rows are missing.

* Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

: I used a strategy for filing in all of the missing values with the mean for that 5-minute interval. First of all, I made a function **'interval2steps'** to get the mean steps for particular 5-minute interval. 
* Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
actdata_na_filled <- merge(actdata, steps_interval, by = 'interval', suffixes = c('', '.y'))
nas <- is.na(actdata_na_filled$steps)
actdata_na_filled$steps[nas] <- actdata_na_filled$steps.y[nas]
actdata_na_filled <- actdata_na_filled[, 1:3]
```

* Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 

```r
steps_date <- aggregate(steps ~ date, data = actdata_na_filled, FUN = sum)
hist(steps_date$steps)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png) 
OR

```r
barplot(steps_date$steps, names.arg = steps_date$date, xlab = 'date', ylab = 'steps')
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 


**mean** = 1.0766189\times 10^{4} steps.

```r
mean(steps_date$steps)
```

```
## [1] 10766.19
```

**median** = 1.0766189\times 10^{4} steps.

```r
median(steps_date$steps)
```

```
## [1] 10766.19
```

* Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

1. The **mean value does NOT differ** from the estimates from the first part of the assignment. 
2. The **median value differs** from the estimates from the first part of the assignment.
3. The impact of imputing missing data on the estimates of the total daily number of steps was generating an **median** value equals the **mean** value.



## Are there differences in activity patterns between weekdays and weekends?
* Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
actdata_na_filled$day <- ifelse(as.POSIXlt(as.Date(actdata_na_filled$date))$wday%%6==0, 'weekend', 'weekday')
actdata_na_filled$day <- factor(actdata_na_filled$day, levels = c('weekday','weekend'))
```


* Make a panel plot containing a time series plot (i.e. type = 'l') of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

```r
plot_data <- aggregate(steps ~ interval + day, actdata_na_filled, mean)
library(lattice)
xyplot(steps ~ interval | factor(day), data=plot_data, aspect=1/2, type='l')
```

![](PA1_template_files/figure-html/unnamed-chunk-15-1.png) 
