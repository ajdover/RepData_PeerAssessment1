# Reproducible Research: Peer Assessment 1


## Load the data 

Data file activity.csv is located in working directory


```r
DataFile <- read.csv("activity.csv")
```
## Preprocess the data 


```r
#convert data
DataFile$date <- as.Date(DataFile$date , format = "%Y-%m-%d")

#create dataframe with total number of steps per day
DataFile.day <- aggregate(DataFile$steps, by=list(DataFile$date), sum)
names(DataFile.day)[1] <-"day"
names(DataFile.day)[2] <-"steps"

#create dataframe with total number of steps per interval
DataFile.interval <- aggregate(DataFile$steps, by=list(DataFile$interval), sum, na.rm=TRUE, na.action=NULL)
names(DataFile.interval)[1] <-"interval"
names(DataFile.interval)[2] <-"steps"

#create dataframe with mean number of steps per interval
DataFile.mean.interval <- aggregate(DataFile$steps, by=list(DataFile$interval), mean, na.rm=TRUE, na.action=NULL)
names(DataFile.mean.interval)[1] <-"interval"
names(DataFile.mean.interval)[2] <-"mean.steps"
```

## What is mean total number of steps taken per day?

# Histogram of total number of steps taken per day

```r
hist(DataFile.day$steps,
     main = "Histogram of the total number of steps taken per day", xlab = "Total number of steps taken per day")
```

![](./PA1_template_files/figure-html/unnamed-chunk-3-1.png) 
# Calculate and report the mean and median number of steps taken per day
Mean number of steps taken per day:

```r
mean(DataFile.day$steps, na.rm = TRUE)
```

```
## [1] 10766.19
```
Median number of steps taken per day:

```r
median(DataFile.day$steps, na.rm = TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

# Time series plot

```r
plot(DataFile.mean.interval$interval, DataFile.mean.interval$mean.steps, type="n",
     main="Time Series Plot per 5-minute Interval",
     xlab = "5=minute Intervals",
     ylab = "Average number of steps taken")
lines(DataFile.mean.interval$interval, DataFile.mean.interval$mean.steps, type="l")
```

![](./PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

# Which 5-minute Interval, on average across all the days in the dataset, contains the maximum number of steps

```r
DataFile.mean.interval[which.max(DataFile.mean.interval$mean.steps),1]
```

```
## [1] 835
```

## Imputing missing values

# Calculate and report the total number of missing values in the dataset 

```r
sum(is.na(DataFile$steps))
```

```
## [1] 2304
```


# Devise a strategy for filling in all the missing values in the dataset

```r
#merge dataframes
DataFile.missing <- merge(DataFile, DataFile.mean.interval, by = "interval", sort=FALSE)
#sort and fill in missing steps with the mean steps
DataFile.missing <- DataFile.missing[with(DataFile.missing, order(date, interval)), ]
DataFile.missing$steps[is.na(DataFile.missing$steps)] <- DataFile.missing$mean.steps[is.na(DataFile.missing$steps)]
#clean up mean column
DataFile.missing$mean.steps <- NULL
#round steps to remove fractional values
DataFile.missing$steps <- round(DataFile.missing$steps, digits = 0)
```

# Create a new dataset that is equal to the original dataset but with the missing values filled in


```r
DataFile.new <- DataFile.missing[,c(2,3,1)]
# create new dataframe with revised total number of steps per day
DataFile.day.new <- aggregate(DataFile.new$steps, by=list(DataFile.new$date), sum)
names(DataFile.day.new)[1] <- "day"
names(DataFile.day.new)[2] <- "steps"
```

# Make a histogram of the total number of steps taken each day.

```r
hist(DataFile.day.new$steps,
     main = "Histogram of the total number of steps taken each day with missing data replaced", xlab = "Total number of steps taken each day")
```

![](./PA1_template_files/figure-html/unnamed-chunk-11-1.png) 

#Calculate and report the mean and median total number of steps taken per day.
Mean number of steps taken per day

```r
mean(DataFile.day.new$steps)
```

```
## [1] 10765.64
```

Median number of steps taken per day

```r
median(DataFile.day.new$steps)
```

```
## [1] 10762
```
# Do thesse values differ from the estimates in the first part of the assignment?
Mean is 0.55 lower than the first part of the assignment
Median is 3.0 lower than the first part of the assignment.
These differences are negligible given the data.

# What is the impact of imputing missing data on the estimates of the total daily number of steps?

The shape of the histogram is similar, but the overall frequencies are higher since the NA data imputed

## Are there differences in activity patterns between weekdays and weekends? Use the new dataset here.

# Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
DataFile.new$weekdays <- factor(format(DataFile.new$date, '%A'))
levels(DataFile.new$weekdays)
```

```
## [1] "Friday"    "Monday"    "Saturday"  "Sunday"    "Thursday"  "Tuesday"  
## [7] "Wednesday"
```

```r
levels(DataFile.new$weekdays) <- list("weekday" = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday"), "weekend" = c("Saturday", "Sunday"))
```

# Make a panel plot containing the Time Series plot 


```r
DataFile.new.mean.interval <- aggregate(DataFile.new$steps, by=list(DataFile.new$weekdays, DataFile.new$interval), mean, na.rm=TRUE, na.action=NULL)
names(DataFile.new.mean.interval)[1] <- "weekday"
names(DataFile.new.mean.interval)[2] <- "interval"
names(DataFile.new.mean.interval)[3] <- "mean.steps"

library(lattice)
#xplot(DataFile.new.mean.interval$mean.steps ~ DataFile.new.mean.interval$interval | DataFile.new interval$weekday,
#      layout=c(1,2)
#      type="l"
#      xlab = "Interval"
#      ylab = "Number of steps")
```

