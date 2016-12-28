# reporducible_research_peer_assesment_1

## Instructions
This scripts assumes that you have changes your working directory to the 
location of the cloned GitHub repo.

## Loading and preprocessing the data
The following loads the activity data and loads necessary libraries.
We also provide a few summary stats of the data.

```r
library(scales)
library(ggplot2)
if(!exists("activity")) {
    activity <- read.csv(unzip("activity.zip"), header = TRUE)
}
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

```r
dim(activity)
```

```
## [1] 17568     3
```

```r
sum(is.na(activity))
```

```
## [1] 2304
```

## What is mean total number of steps taken per day?
First we calculate the total number og steps taken for each day in the entire 
dataset and reformat the dataset a little.

```r
stepsPerDay <- as.data.frame.table(tapply(activity$steps, activity$date, sum))
names(stepsPerDay) <- c('date', 'steps')
stepsPerDay$date <- as.Date(stepsPerDay$date)
```

We then plot the data using ggplot.

```r
ggplot(data = stepsPerDay, aes(steps)) + 
    geom_histogram() +
    xlab("Steps") + ylab("Frequency") + ggtitle("Distribution of Daily Steps")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

```
## Warning: Removed 8 rows containing non-finite values (stat_bin).
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

We then report the median and average daily step count.

## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
