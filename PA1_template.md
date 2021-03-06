# Reporducible Research Peer Assesment 1

## Instructions
This scripts assumes that you have changed your working directory to the 
location of the cloned GitHub repo.

## Loading and preprocessing the data
The following loads the activity data and all necessary libraries.

```r
library(scales)
library(ggplot2)
library(lattice)
library(knitr)
if(!exists("activity")) {
    activity <- read.csv(unzip("activity.zip"), header = TRUE)
}
```

## What is mean total number of steps taken per day?
First we calculate the total number og steps taken for each day in the entire 
dataset and reformat the dataset a little.

```r
stepsPerDay <- as.data.frame.table(
                    tapply(activity$steps, activity$date, sum, na.rm = TRUE))
names(stepsPerDay) <- c('date', 'steps')
stepsPerDay$date <- as.Date(stepsPerDay$date)
```

We then plot the data using ggplot2.

```r
ggplot(data = stepsPerDay, aes(steps)) + 
    geom_histogram() +
    xlab("Steps") + ylab("Frequency") + ggtitle("Distribution of Daily Steps")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

We then report the median and average daily step count.

```r
median(stepsPerDay$steps, na.rm = TRUE)
```

```
## [1] 10395
```

```r
mean(stepsPerDay$steps, na.rm = TRUE)
```

```
## [1] 9354.23
```

## What is the average daily activity pattern?
Below we are summarising the step count by interval and then plotting the
timeseries.

```r
dailyPattern <- aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
ggplot(data = dailyPattern, aes(y = steps, x = interval)) + 
    geom_line(colour = "blue") +
    xlab("Minute Interval") + ylab("Steps") +
    ggtitle("Daily Pattern of Step Count")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

We identify the interval with max number of steps below.

```r
dailyPattern[dailyPattern$steps == max(dailyPattern$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

## Imputing missing values
Here we report the number of NAs in the data set.

```r
sum(is.na(activity))
```

```
## [1] 2304
```

My strategy is to take the median steps per day AND interval which should
account better for interval variation between days of the week.
We return the same kind of data frame but with NAs imputed.

```r
activityNew <- activity
activityNew$day <- weekdays(as.Date(activity$date))
imputation <- aggregate(steps ~ interval + day, data = activityNew, median,
                        na.rm = TRUE)
activityNew <- merge(x = activityNew, y = imputation, 
                     by.x = c("day", "interval"), by.y = c("day", "interval"))
names(activityNew) <- c("day","interval","steps_original","date","steps_impute")
for (i in 1:length(activityNew$steps_original)) {
    if (is.na(activityNew$steps_original[i])) {
        activityNew$steps_original[i] <- activityNew$steps_impute[i]
        }
}
activityNew <- activityNew[,c(3,4,2)]
names(activityNew) <- c("steps", "date", "interval")
```

We then create a histogram and report median and mean.

```r
data <- aggregate(steps ~ date, data = activityNew, sum)
ggplot(data = data, aes(steps)) + 
    geom_histogram() +
    xlab("Steps") + ylab("Frequency") + 
    ggtitle("Distribution of Daily Steps w/ Imputed Missing Values")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
median(data$steps)
```

```
## [1] 10395
```

```r
mean(data$steps)
```

```
## [1] 9705.238
```

The median is the same as before because missing values are imputed using the
median of that day-interval combination. The mean is slightly higher because we 
previous zero-days are now being limited due to the imputation of missing data 
points. The change is not very dramatic and the distribution remains quite
similar.

## Are there differences in activity patterns between weekdays and weekends?
Here we are using the data set WITH imputed values and later computing the mean
of interval - weekday/weekend combinations, before plotting the two time series.

```r
weekDays <- activityNew
weekDays$day <- factor(weekdays(as.Date(activityNew$date)))
levels(weekDays$day) <- list(
    weekend = c("Saturday","Sunday"),
    weekday = c("Monday","Tuesday","Wednesday","Thursday","Friday")
)
weekDays <- aggregate(steps ~ day + interval, data = weekDays, mean)
xyplot(steps ~ interval | day,
       data = weekDays,
       type = "l",
       aspect = 1/2,
       xlab = "Interval",
       ylab = "Steps",
       main = "Steps by Weekday and Weekend")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
