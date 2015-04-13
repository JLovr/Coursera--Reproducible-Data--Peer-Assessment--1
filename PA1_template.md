# Analysis of Daily Step Activity as Measured by Activity Monitoring Devices

The purpose of this analysis is give the student the opportunity to gain practice with producing R Markdown documents in an acceptable form.  To that effect we are provided data on movement recorded from devices worn by unknown individuals who record these movements for a variety of reasons. 


**Data**

The data for this assignment can be downloaded from the course web site Dataset:

[Data for Course Peer Assessment 1](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K] 

*Source: Johns Hopkins Course Reproducible Research Peer Assessment #1 from the Readme - R. Peng  *

>This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.
>The variables included in this dataset are:

> * steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)
> * date: The date on which the measurement was taken in YYYY-MM-DD format
> * interval: Identifier for the 5-minute interval in which measurement was taken

>The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

## Load and Process the Data

```r
## load required libraries
library(ggplot2)
##library(data.table)

##read data, do preliminary eximination
raw.data<-read.csv("activity.csv")
head(raw.data)
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
nrow(raw.data)
```

```
## [1] 17568
```

```r
summary(raw.data)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```

```r
## set date context for easier processing
wdata<-raw.data
wdata$date<-as.Date(wdata$date,"%Y-%m-%d")
```

## What is mean total number of steps taken per day?


```r
#generate aggregate dataset of steps by date
a<-aggregate(steps~date,wdata,sum)

#generate the histogram
qplot(a$date,a$steps,geom="histogram",stat="identity",xlab="Dates",ylab="Steps",main="Total Steps by Date")
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-1-1.png) 

```r
# generate total mean and median
amedian <- sprintf("%0.6f",median(a$steps))
amean <- sprintf("%0.6f",mean(a$steps))
```

- Median total steps per day: 10765.000000
- Mean total steps per day: 10766.188679

## What is the average daily activity pattern?


```r
#aggregate data by interval across all days
b<-aggregate(steps~interval,wdata,mean)
summary(b)
```

```
##     interval          steps        
##  Min.   :   0.0   Min.   :  0.000  
##  1st Qu.: 588.8   1st Qu.:  2.486  
##  Median :1177.5   Median : 34.113  
##  Mean   :1177.5   Mean   : 37.383  
##  3rd Qu.:1766.2   3rd Qu.: 52.835  
##  Max.   :2355.0   Max.   :206.170
```

```r
# generate plot
qplot(b$interval,b$steps,geom="line",xlab="Interval",ylab="Steps",main="Total Steps by Interval")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
bmax<-b$interval[which.max(b$steps)]
```

Maximum number of steps, on average, during the day, occurred during the 835 interval.

## Addressing missing values

```r
## determine # of cases with missing values
ccases<-complete.cases(wdata$steps)
nacases<-nrow(wdata)-nrow(wdata[ccases,])
```

We have 2304 records where there is missing data.
There are a number of ways to address the missing values.  One could apply a daily mean for every missing interval, or apply the interval average across all missing days.  The problem is that the missing data could also be well outside the norm.  With that observation noted, it seems that filling in the interval with the mean for that interval across the period will distort the data the least (given the variation we see over the daily recording period).


```r
c<-wdata
work<-!complete.cases(c)
work1<-which(work)

## we use the z var to build a results matrix for debugging use only
##z<-NULL
for (v in work1) {
    w<-b[b$interval==c$interval[v],]
##  z<-rbind(z,cbind(c$interval[v],w$interval,c$steps[v],w$steps))
    c$steps[v]<-w$steps
}
## for debugging purposes:
##summary(c)
##z

#aggregate data by date across all intervals
d<-aggregate(steps~date,c,sum)
summary(d)
```

```
##       date                steps      
##  Min.   :2012-10-01   Min.   :   41  
##  1st Qu.:2012-10-16   1st Qu.: 9819  
##  Median :2012-10-31   Median :10766  
##  Mean   :2012-10-31   Mean   :10766  
##  3rd Qu.:2012-11-15   3rd Qu.:12811  
##  Max.   :2012-11-30   Max.   :21194
```

```r
# generate plot
#generate the histogram
qplot(d$date,d$steps,geom="histogram",stat="identity",xlab="Dates",ylab="Steps",main="Total Steps by Date")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
dmax<-d$interval[which.max(d$steps)]

# generate total mean and median
dmedian <- sprintf("%0.6f",median(d$steps))
dmean <- sprintf("%0.6f",mean(d$steps))

# more debugging stuff to make sure we didn't mess up valid measurements
#c[c$date==as.Date("2012-10-02"),]
#wdata[wdata$date==as.Date("2012-10-02"),]
#c[c$date==as.Date("2012-11-15"),]
#wdata[wdata$date==as.Date("2012-11-15"),]
```
### Results of addressing the NA step values
As modified:

- Median total steps per day 10766.188679.

- Mean total steps per day 10766.188679.

Compared to the original values:

- Median total steps per day 10765.000000.

- Mean total steps per day 10766.188679.

Note the difference in the median values.  

This shows that, by employing our solution to the NA
value issue, we created a smoothing effect which didn't alter the mean value, but did change the median value, essentiall making the median converge on the mean.

## Are there differences in activity patterns between weekdays and weekends?


```r
## add factor 'daytype' - values weekday or weekend
e<-c
e$daytype<-ifelse(weekdays(e$date) %in% c("Sunday","Saturday"),"weekend","weekday")

##do plot
e11<-e$daytype=="weekend"
e21<-e$daytype=="weekday"
e1<-e[e11,]
e2<-e[e21,]
f1<-cbind(aggregate(steps~interval,e1,mean),daytype="weekend")
f2<-cbind(aggregate(steps~interval,e2,mean),daytype="weekday")
f<-rbind(f1,f2)

p<-ggplot(f,aes(interval,steps))+geom_line()
p<-p+ylab("Number of Steps")
p<-p+facet_wrap(~daytype,nrow=2)
print(p)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 


From the plot, we can see that there is noticeably less activity on weekdays between the 900 and 1700 interval marks, possibly corresponding to the lower movement activity of some types of day shift workers on weekdays.

One also notes the increased activity between the 500 and 900 interval marks on weekdays possibly due to the daily ritual of preparing for and arriving at work.
