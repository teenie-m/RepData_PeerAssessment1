Human Activity Monitoring Data Analysis
========================================================

First, read the data in using the 'read.csv' function.

```r
activity <- read.csv("activity.csv")
```

The tapply function can then be used to summarize the number of steps by date.

```r
n <- tapply(activity$steps, activity$date, mean, na.rm = TRUE)
hist(n, main = "Steps Per Day", xlab = "Number of Steps", col = "lavender")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

The average number of steps per day seem symetrical in the histogram, and indeed, the mean and median are very similar.

```r
mean(n, na.rm = TRUE)
```

```
## [1] 37.38
```

```r
median(n, na.rm = TRUE)
```

```
## [1] 37.38
```

We could also summarize the steps data by time point instead of by date. Some interesting patterns emerge.

```r
m <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
plot(m, type = "l", xlab = "Time (in 5 minute bins)", ylab = "Average Number of Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

The names function can be used to retrieve the time point with the maximum number of steps from the 'm' object.

```r
names(m[m==max(m)])
```

```
## [1] "835"
```

Unfortunately, there is a lot of missing data in this data set.

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```

Using the 'm' object we created previously, we can create a new data frame (called 'nas.imputed') and fill in all NAs with the mean number of steps for that time of day. 


```r
nas.imputed <- activity

for (row in 1:nrow(nas.imputed)) {
  time <- as.character(nas.imputed[row, 3])
  if (is.na(nas.imputed[row, 1])) {
      nas.imputed[row, 1] <- m[[time]]
  }
}

head(nas.imputed)
```

```
##     steps       date interval
## 1 1.71698 2012-10-01        0
## 2 0.33962 2012-10-01        5
## 3 0.13208 2012-10-01       10
## 4 0.15094 2012-10-01       15
## 5 0.07547 2012-10-01       20
## 6 2.09434 2012-10-01       25
```

As we can see, this method of filling in NAs has little impact on the total number of steps per day.


```r
l <- tapply(nas.imputed$steps, nas.imputed$date, mean, na.rm = TRUE)
hist(l, main = "Steps Per Day (Missing Values Imputed)", xlab = "Number of Steps", col = "lavender")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

```r
mean(l, na.rm = TRUE)
```

```
## [1] 37.38
```

```r
median(l, na.rm = TRUE)
```

```
## [1] 37.38
```

Finally, we can ask whether activity changes between weekends and weekdays. 


```r
nas.imputed$date <- as.Date(nas.imputed$date)
nas.imputed$weekend <- ifelse(weekdays(nas.imputed$date) == "Saturday" | weekdays(nas.imputed$date) == "Sunday", "weekend", "weekday")
```

