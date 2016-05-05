# Reproducible Research: Peer Assessment 1
________________________________________  

```r
library(ggplot2)
```
Load neccesary libraries for ploting

### *1. Loading and preprocessing the data*
__________________________________________

The following section will load data.

```r
unzip(zipfile = "activity.zip")
data <- read.csv("activity.csv")
```


### *2. What is mean total number of steps taken per day?*
__________________________________________

```r
total_steps_day <- tapply(data$steps,data$date, sum, na.rm = TRUE)
qplot(total_steps_day, binwidth=1000,xlab = "total number of steps taken each day", ylab = "Frequency")
```

![](PA1_template_files/figure-html/Calualation: Mean total steps per day-1.png)<!-- -->

```r
data_mean <- mean(total_steps_day, na.rm = TRUE)
trunc(data_mean)
```

```
## [1] 9354
```

```r
data_median <- median(total_steps_day, na.rm = TRUE)
trunc(data_median)
```

```
## [1] 10395
```

The mean of steps taken each day is : 9354   
The median steps taken for each day is : 1.0395\times 10^{4}
  
  
### *3. What is the average daily activity pattern?*
__________________________________________

```r
average <- aggregate(list(steps = data$steps), by = 
                             list(interval = data$interval), FUN=mean, 
                     na.rm=TRUE)

ggplot(average, aes(interval, steps)) + geom_line(color = "blue", size = 0.7) + 
        labs(title = "Average Daily Activity Chart", x = "5-minute intervals",
             y = "Average Number of Steps Taken")
```

![](PA1_template_files/figure-html/Calulation: avg daily activity pattern-1.png)<!-- -->

```r
max <- average[which.max(average$steps),]
print(max)
```

```
##     interval    steps
## 104      835 206.1698
```

The interval that has the maximum number of steps is: 835   

  
  
### *4. Imputing missing values*
__________________________________________

```r
result4a <- sum(is.na(data$steps))
```

Total number of missing values is 2304.


```r
insert_func <- function(interval,steps){
        if(is.na(steps)){
              average[which(average$interval==interval),"steps"]
        }else{
              steps
        }
}
```

The above code is for inserting value into missing fields. If value is missing, 
insert average daily steps for that interval. If value already exist, use 
original value.


```r
filled_data <- data
filled_data$steps  <- mapply(insert_func,filled_data$interval, filled_data$steps)
```

Create Histogram for new total number of steps.


```r
totalSteps <- tapply(filled_data$steps, filled_data$date, FUN=sum)
qplot(totalSteps, binwidth = 1000, xlab = "total number of steps taken each day")
```

![](PA1_template_files/figure-html/Create histogram-1.png)<!-- -->



```r
na_mean <- mean(totalSteps, na.rm = TRUE)  
trunc(na_mean)
```

```
## [1] 10766
```

```r
na_median <- median(totalSteps, na.rm=TRUE)
trunc(na_median)
```

```
## [1] 10766
```
The new mean is : 1.0766\times 10^{4}  
The new median is : 1.0766\times 10^{4}  
  
There is a difference between the mean and the median value of imputed and non-imputed value as formula for both mean and median value involves the division operator. Any value introduced into division operator will cause data to be skewed.

### *5. Are there differences in activity patterns between weekdays and weekends?*
__________________________________________


```r
filled_data$date <- as.Date(filled_data$date)
filled_data$day <- sapply(filled_data$date, FUN= function(date) {
        if(weekdays(date) %in% c("Monday", "Tuesday", "Wednesday", 
                       "Thursday", "Friday")) "weekday"
        else if(weekdays(date) %in% c("Saturday", "Sunday")) "weekend"
        else NA
})
```

The above code is used to insert an extra column called day into imputed dataset to indicate if the date is a weekend or a weeday.  


```r
average <- aggregate(steps ~  interval + day, data = filled_data, mean)
ggplot(average, aes(interval, steps)) + geom_line() + 
        facet_grid(day ~ .) + xlab("5-minute interval") +
        ylab("Number of steps")
```

![](PA1_template_files/figure-html/Calulating average steps across interval and day-1.png)<!-- -->
  
  
  
------------------------------------ **THE END** -------------------------------------




