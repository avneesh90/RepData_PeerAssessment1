# RepData_PeerAssessment_1
avneesh90  
Sunday, October 19, 2014  

**Show any code that is needed to**

    1. Load the data (i.e. read.csv() )

```r
## read file
x=read.csv("activity.csv")
```
  
  2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
## change to date data type
x[,2]=as.Date(as.character(x[,2]))
```

**What is mean total number of steps taken per day?**

For this part of the assignment, you can ignore the missing values in the dataset.

    1. Make a histogram of the total number of steps taken each day

```r
library(plyr)
library(ggplot2)

z1= ddply(x,"date", summarise, stday=sum(steps))
ggplot(z1,aes(x=factor(date), y=stday)) + geom_bar(stat="identity") 
```

```
## Warning: Removed 8 rows containing missing values (position_stack).
```

![](./PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

    2. Calculate and report the mean and median total number of steps taken per day

```r
##mean
mean(z1[,2],na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
## median
median(z1[,2],na.rm=TRUE)
```

```
## [1] 10765
```

**What is the average daily activity pattern?**

    1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
z2= ddply(x,"interval", summarise, stint=mean(steps,na.rm=TRUE))
ggplot(z2,aes(x=interval, y=stint)) + geom_line()
```

![](./PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
    
    2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps


```r
##interval containing max steps in ainterval
z2[which.max(z2[,2]),1]
```

```
## [1] 835
```

**Inputing missing values**

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

    1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
##no of missing values
sum(is.na(x[,1]))
```

```
## [1] 2304
```
   
   2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
    *using the mean for that five min interval for missing values*
   
   3. Create a new dataset that is equal to the original dataset but with the missing data filled in.    

```r
# z3 new data set containing missing values
z3=x
for (i in 1:17568){
  if(is.na(z3[i,1]))  {
    z3[i,1]<-z2[(if(i%%288) i%%288 else 288), 2]
  } 
}
```
    
    3. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
z4= ddply(z3,"date", summarise, stday=sum(steps))
ggplot(z4,aes(x=factor(date), y=stday)) + geom_bar(stat="identity")
```

![](./PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
##mean
mean(z4[,2],na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
## median
median(z4[,2],na.rm=TRUE)
```

```
## [1] 10766.19
```

-*Mean remains the same as mean values were used for missing values*
-*Median value has changed now to one of the values missing earlier and hence is same as mean*

**Are there differences in activity patterns between weekdays and weekends?**

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

    1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
z3$weekday= "weekday"
for(i in 1:17568){
  if(weekdays(z3[i,2])=="Saturday" || weekdays(z3[i,2])=="Sunday" ) z3[i,4]="weekend"
}
z3$weekday=as.factor(z3$weekday)
```
    
    2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:

```r
z5= ddply(z3,c("weekday","interval"), summarise, stint=mean(steps,na.rm=TRUE))

ggplot(z5, aes(x=interval,y=stint)) + facet_wrap("weekday",nrow=2) + geom_line()
```

![](./PA1_template_files/figure-html/unnamed-chunk-11-1.png) 




