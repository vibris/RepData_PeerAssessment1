---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---
# Reproducible Research: Peer Assessment 1

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Loading the data

The data is loaded:
```r
df <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?

1. The total number of steps taken per day is calculated:
```r
tdf <- aggregate(df$steps, by = list(df$date), FUN = sum)
```

2. A histogram of the total number of steps taken each day is the following:
```r
hist(tdf$x, xlab="Number of steps taken per day", main="A histogram of the total number of steps taken each day")
```

3. The **mean** and **median** total number of steps taken per day are calculated:
```r
smean <- mean(tdf$x, na.rm=T)
smedian <- median(tdf$x, na.rm=T)
abline(v=smean, col="red")
abline(v=smedian, col="blue")
legend('topright', c("Mean","Median"), lty=1,  col=c("red","blue"), cex=0.8 )
```

The **mean** and **median** are slightly different. The **mean** is 1.0766189 &times; 10<sup>4</sup> and the **median** is 10765.

## What is the average daily activity pattern?

1. A time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis) is made:
```r
mdf <- aggregate(df$steps, by = list(df$interval), FUN = mean, na.rm=T)
plot(mdf$"Group.1", mdf$x, type="l",xlab="5-minute interval", ylab="number of steps taken, averaged across all days", main="A time series plot")
```

2. The 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps is 835

## Imputing missing values

1. The total number of missing values in the dataset (i.e. the total number of rows with `NA`s) is:2304

2. We would use the mean for that 5-minute interval,  averaged across all days.

3. A new dataset that is equal to the original dataset but with the missing data filled in is created.
```r
newdf<-df
t2df <- merge(newdf[is.na(newdf$steps),], mdf, by.x="interval", by.y="Group.1")
t2df$steps <- NULL
colnames(t2df)[names(t2df)=="x"]<- "steps"
t2df<-t2df[c(3,2,1)]
newdf[is.na(newdf$steps),]<-t2df
```

4. A histogram of the total number of steps taken each day is the following:
```r
t3df <- aggregate(newdf$steps, by = list(newdf$date), FUN = sum)
hist(t3df$x, xlab="Number of steps taken per day", main="A histogram of the total number of steps taken each day")
smean <- mean(t3df$x)
smedian <- median(t3df$x)
abline(v=smean, col="red")
abline(v=smedian, col="blue")
legend('topright', c("Mean","Median"), lty=1,  col=c("red","blue"), cex=0.8 )
```

The **mean** and **median** are the same and are 1.0766189 &times; 10<sup>4</sup>.

## Are there differences in activity patterns between weekdays and weekends?

1. A new factor variable in the dataset is created with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day:
```r
   newdf$day<- format(as.Date(newdf$date),"%w")
   newdf$day[newdf$day=="0"|newdf$day=="6"] <- "weekend"
   newdf$day[newdf$day!="weekend"] <- "weekday"
   newdf$day <- as.factor(newdf$day)
```

1. A panel plot containing a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis) is the following:
```r
   par(mfrow=2:1)
   new2df<-newdf[newdf$day=="weekday",]
   m2df <- aggregate(new2df$steps, by = list(new2df$interval), FUN = mean)
   plot(m2df$"Group.1", m2df$x, type="l",xlab="5-minute interval", ylab="number of steps", main="Weekday")
   
   new2df<-newdf[newdf$day=="weekend",]
   m2df <- aggregate(new2df$steps, by = list(new2df$interval), FUN = mean)
   plot(m2df$"Group.1", m2df$x, type="l",xlab="5-minute interval", ylab="number of steps", main="Weekend")
```
