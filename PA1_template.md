# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Show any code that is needed to

Load the data (i.e. read.csv())

Process/transform the data (if necessary) into a format suitable for your analysis


```r
activity <- read.csv("activity.csv")
## convert the date variable from a factor to a date
activity$date <- as.Date(as.character.Date(activity$date))
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

Make a histogram of the total number of steps taken each day

Calculate and report the mean and median total number of steps taken per day

```r
## Use the aggregate function to sum the number of steps in a day into a data fram
sumSteps <- aggregate(steps~date, activity, sum)
## create histogram of the total number of steps per day
hist(sumSteps$steps, col="red", main="Total Number of Steps Per Day", xlab="Steps Per Day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 


Calculate the mean and median number of steps

```r
mean(sumSteps$steps)
```

```
## [1] 10766
```

```r
median(sumSteps$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
meanSteps <- aggregate(steps~interval, activity, mean)
plot(meanSteps$interval, meanSteps$steps, type = "l", xlab = "5-Minute Intervals",  ylab = "Steps", main = "Average Number of Steps All Days")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
meanSteps[which.max(meanSteps$steps), 'interval']
```

```
## [1] 835
```


## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)



```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
## Strategy: replace missing values with the means for each time step

## Create new data from as copy of activity data frame

complete <- activity

## if an na is in the steps column, replace it with the corresponding value from the meanSteps data frame
for(i in 1:nrow(activity)){
    if(is.na(activity[i,1])){
        complete[i,1]=meanSteps[meanSteps$interval==activity[i,3],2]
    }
}
```


Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
comSteps <- aggregate(steps~date, complete, sum)
hist(comSteps$steps, col="red", main="Total Number of Steps Per Day", xlab="Steps Per Day")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

```r
mean(comSteps$steps)
```

```
## [1] 10766
```

```r
median(comSteps$steps)
```

```
## [1] 10766
```


The mean of the average number of steps in the complete data frame is the same as the mean from the original data frame. The median is only slightly shifted.
## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:



```r
weekfact <- factor(
        ifelse(weekdays(complete$date) %in% c("Saturday", "Sunday"), 
               "weekend", "weekday"),
        levels = c("weekend", "weekday"))
complete <- cbind(complete, weekfact)
library(lattice)
splitSteps <- aggregate(steps ~ interval + weekfact, complete, mean)
xyplot(steps ~ interval | weekfact, data = splitSteps, type = "l", layout = c(1, 2))
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 


