# Week 5 Activity Monitoring



####Read the data into R

```r
dataWithNAs <- read.csv("activity.csv")
```

####Drop the NA rows, get date into the Data Object form, and create data sets by date and by interval respectively

```r
df <- na.omit(dataWithNAs)
byDate <- df[, c("steps", "date")] 
byInterval <- df[, c("steps", "interval")]
byDate$date <- as.Date(byDate$date)
```

####Group By Date. i.e., sum the steps per day.  And plot the distribution of steps per day in a histogram

```r
dfByDate <- aggregate(byDate$steps, by=list(byDate$date), FUN=sum, na.rm=TRUE)
names(dfByDate) = c("date", "steps")
hist(dfByDate$steps, xlab="Steps per day", ylab="Count", main="Steps per Day", col="red")
```

![](activity_files/figure-html/GroupByDate-1.png)<!-- -->

####Print out the mean and median steps per day

```r
stepsMean <- mean(dfByDate$steps)        
stepsMedian <- median(dfByDate$steps)    
cat("Mean Steps per day: ", stepsMean, "\n")
```

```
## Mean Steps per day:  10766.19
```

```r
cat("Median Steps per day: ",  stepsMedian, "\n")
```

```
## Median Steps per day:  10765
```

####Group By Interval, i.e., average the steps per interval. And plot a time-series for the average steps per interval.

```r
dfBy5Interval <- aggregate(byInterval$steps, by=list(byInterval$interval), FUN=mean, na.rm=TRUE)
names(dfBy5Interval) = c("interval", "steps")
plot(dfBy5Interval$interval, dfBy5Interval$steps, type="l", xlab="Interval", ylab="Average Steps", 
     main="Average Steps per 5 minute Interval", col="green")
```

![](activity_files/figure-html/GroupByInterval-1.png)<!-- -->

####Print out the interval with the highest average steps

```r
intervalMax <- dfBy5Interval$interval[which.max(dfBy5Interval$steps)]
cat("The interval with the maximum average number of steps: ",  intervalMax, "\n")
```

```
## The interval with the maximum average number of steps:  835
```

####Print out the numer of NAs.  The number of rows with missing data

```r
numberNAs <- sum(is.na(dataWithNAs))
cat("The number of missing data: ",  numberNAs, "\n")
```

```
## The number of missing data:  2304
```

####Impute the missing data with the average steps per 5 minute interval

```r
imputedData <- dataWithNAs
imputedData$steps[is.na(imputedData$steps)] <- mean(df$steps)  
```

####Imputed Data set. Group By Date, i.e., sum the steps per day.  And plot the distribution of steps per day in a histogram

```r
imputedbyDate <- imputedData[, c("steps", "date")]
imputedDataByDate <- aggregate(imputedbyDate$steps, by=list(imputedbyDate$date), FUN=sum, na.rm=TRUE)
names(imputedDataByDate) = c("date", "steps")
hist(imputedDataByDate$steps, xlab="Step per day", ylab="Count", main="Steps per Day (Imputed)", col="red")
```

![](activity_files/figure-html/GroupByDateImpute-1.png)<!-- -->

####Print out the mean and median steps per day of the imputed data

```r
stepsImputedMean <- mean(imputedDataByDate$steps)        
stepsImputedMedian <- median(imputedDataByDate$steps)    
cat("Imputed Mean Steps per day: ", stepsImputedMean, "\n")
```

```
## Imputed Mean Steps per day:  10766.19
```

```r
cat("Imputed Median Steps per day: ",  stepsImputedMedian, "\n")
```

```
## Imputed Median Steps per day:  10766.19
```
####The impact of imputing missing values with the 5 minute interval mean
#####By imputing the missing data with the mean, the median moved to the mean, and both are equal the mean of the data when the missing values were dropped

####Create a factor with levels: Weekday, Weekend

```r
library(timeDate)
```

```
## Warning: package 'timeDate' was built under R version 3.3.3
```

```r
imputedData$Day <- factor(isWeekday(imputedData$date), 
                          levels=c(FALSE, TRUE), labels=c('Weekend', 'Weekday')) 
```

####Group By DayType and Inerval, i.e., get the average steps per day type and interval

```r
dfByDayTypeAndInterval <- aggregate(imputedData$steps, by=list(imputedData$Day,imputedData$interval), FUN=mean, na.rm=TRUE)
names(dfByDayTypeAndInterval) = c("dayType", "interval", "steps")
```

####plot the disribution of the average steps by interval. Draw 2 panels (facets) one for the weekday and a second one for the weekend

```r
library(ggplot2)
qplot(interval, steps, data=dfByDayTypeAndInterval, xlab="Interval", ylab="Average Steps",
      main="Average Steps on Weekdays and Weekends", geom="line", facets=dayType ~ .)
```

![](activity_files/figure-html/Plot2Panels-1.png)<!-- -->

####Difference between activity patterns during the weekdays and the weekend
#####The weekday activity has a spike of more than 200 steps around the 800 time interval, The weekend activity swings above and below 75 steps over the intervals
#####PATTERN: On weekdays there is a rush-hour. People are rushing at 200 steps around the 800 time interval. But, on weekends people walk leisurely at average of around 75 steps per 5 minute interval all day.

