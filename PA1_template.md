# Reproducible Research: Peer Assessment 1
________________________________________  
 
 ```r
 library(ggplot2)
 ```
 
 ```
 ## Warning: package 'ggplot2' was built under R version 3.2.5
 ```
  

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

![](PA1_template_files/figure-html/Calualation Part 1-1.png)

```r
data_mean <- mean(total_steps_day, na.rm = TRUE)
data_median <- median(total_steps_day, na.rm = TRUE)
```

The mean of steps taken each day is : 9354.2295082   
The median steps taken for each day is : 10395

### *3. What is the average daily activity pattern?*
__________________________________________

```r
average <- aggregate(list(steps = data$steps), by = list(interval = data$interval), FUN=mean, na.rm=TRUE)

ggplot(average, aes(interval, steps)) + geom_line(color = "blue", size = 0.7) + labs(title = "Average Daily Activity Chart", x = "5-minute intervals", y = "Average Number of Steps Taken")
```

![](PA1_template_files/figure-html/Calulation Part 2-1.png)

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



```r
filled_data <- data
filled_data$steps  <- mapply(insert_func,filled_data$interval, filled_data$steps)
```

Create Histogram


```r
totalSteps <- tapply(filled_data$steps, filled_data$date, FUN=sum)
qplot(totalSteps, binwidth = 1000, xlab = "total number of steps taken each day")
```

![](PA1_template_files/figure-html/create histogram-1.png)


```r
na_mean <- mean(totalSteps, na.rm = TRUE)
na_mean
```

```
## [1] 10766.19
```

```r
na_median <- median(totalSteps, na.rm=TRUE)
na_median
```

```
## [1] 10766.19
```


### *5. Are there differences in activity patterns between weekdays and weekends?*
__________________________________________





