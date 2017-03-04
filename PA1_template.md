# **Reproducible Research: Peer Assessment 1**


## **Loading and preprocessing the data**
###Show any code that is needed to
###1) Load the data (i.e. read.csv())
```[r]
fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"  
download.file(fileUrl, destfile="activity.zip", mode="wb")  
unzip(zipfile="activity.zip", exdir="activity")  
data <- read.csv("activity.csv")  
```

###2) Process/transform the data (if necessary) into a format suitable for your analysis  
```[r]
library(ggplot2)  
library(lattice)  
library(dplyr)  
```


## **What is mean total number of steps taken per day?**
###For this part of the assignment, you can ignore the missing values in the dataset.
###1) Calculate the total number of steps taken per day
```[r]
TotalStepsPerDay <- aggregate(steps ~ date, data, sum, na.rm=TRUE)  
```

###2) If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
###Plot Histogram
```[r]
hist(TotalStepsPerDay$steps, main="Total Steps per Day", xlab="Number of Steps", ylab="Frequency")  
```
image: ![](C:/Users/Jieru/Desktop/Data Science/05 Reproducible Research/Week 2/RepData_PeerAssessment1/histo1.png)


###3) Calculate and report the mean and median of the total number of steps taken per day  
```[r]
MeanStepsPerDay <-mean(TotalStepsPerDay$steps)  
MeanStepsPerDay  
[1] 10766.19  

MedianStepsPerDay <-median(TotalStepsPerDay$steps)  
MedianStepsPerDay  
[1] 10765  
```


## **What is the average daily activity pattern?**
###1) Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)  
```[r]
StepsByInterval <- aggregate(steps ~ interval, data, mean)  
plot(StepsByInterval$interval,StepsByInterval$steps, type="l", xlab="5-minute Interval", ylab="Number of Steps", main="Average Number of Steps per 5-minute Interval")  
```
image: ![](C:/Users/Jieru/Desktop/Data Science/05 Reproducible Research/Week 2/RepData_PeerAssessment1/plot1.png)


###2) Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?  
```[r]
MaxInterval <- StepsByInterval[which.max(StepsByInterval$steps),1]  
MaxInterval  
[1] 835  
```


## **Imputing missing values**
###Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.
###1) Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)  
```[r]
MissingValues<-sum(is.na(data[,1]))  
MissingValues  
[1] 2304  
```

###2) Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
*Using mean for 5-minute interval to replace missing values*  
```[r]
MeanStepsByInterval <- mean(StepsByInterval$steps)  
MeanStepsByInterval  
[1] 37.3826
```

###3) Create a new dataset that is equal to the original dataset but with the missing data filled in.  
```[r]
NewDataSet<-data  
NewDataSet$steps[is.na(NewDataSet$steps)] <- MeanStepsByInterval  
```

###4) Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?  
```[r]
TotalStepsEachDay <- aggregate(steps ~ date, NewDataSet, sum, na.rm=TRUE)  
hist(TotalStepsEachDay$steps, main="Total Steps Per Day(adjusted)", xlab="Number of Steps", ylab="Frequency")  
```
image: ![](C:/Users/Jieru/Desktop/Data Science/05 Reproducible Research/Week 2/RepData_PeerAssessment1/histo2.png)  

```[r]
MeanTotalStepsPerDay <- mean(TotalStepsEachDay$steps)  
MeanTotalStepsPerDay  
[1] 10766.19  

MedianTotalStepsPerDay <- median(TotalStepsEachDay$steps)  
MedianTotalStepsPerDay  
[1] 10766.19  
```

>The mean values does not differ from the first part of the assignment since the replacement of missing values was replaced by the mean total steps each day. On the other hand, the median values does increased as compared to the first part of the assignment.



## **Are there differences in activity patterns between weekdays and weekends?**
###For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
###1) Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.  
```[r]
NewDataSet$date <- as.Date(NewDataSet$date)  
NewDataSet$days <- weekdays (NewDataSet$date)  
NewDataSet$weekends <- as.factor(ifelse(NewDataSet$days == "Saturday" | NewDataSet$days == "Sunday", "weekends", "weekdays"))  
```

###2) Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.  
```[r]
NewPlot <- aggregate(steps ~ interval + weekends, NewDataSet, mean)  
xyplot(steps ~ interval | factor(weekends), data=NewPlot, aspect=1/3, type="l", xlab="5-minute Interval", ylab="Average Steps Taken", main="Average Steps per 5-minute Interval")  
```
image: ![](C:/Users/Jieru/Desktop/Data Science/05 Reproducible Research/Week 2/RepData_PeerAssessment1/plot2.png)  
