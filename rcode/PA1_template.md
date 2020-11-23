R Markdown
----------

This is an R Markdown document.It contains all steps that I did with
dataset

1- First loading data

    library(dplyr)

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    library(ggplot2)
     myDataSet <- read.csv('data/activity.csv')

2- Converting data variable from character to data

      myData <- transform(myDataSet , date = as.Date(date))

3- Histogram of the total number of steps taken each day

    daysWithTotalSteps <- summarize(group_by(myData,date),stepsTotal = sum(steps,na.rm =  TRUE))

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ggplot(daysWithTotalSteps, aes(date, stepsTotal)) + geom_bar(stat="identity")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

4-Mean and median number of steps taken each day

    daysWithMainAndMEdianSteps <- summarize(group_by(myData,date),stepsMean = mean(steps,na.rm =  TRUE),stepsMedian = median(steps,na.rm =  TRUE))

    ## `summarise()` ungrouping output (override with `.groups` argument)

    daysWithMainAndMEdianSteps

    ## # A tibble: 61 x 3
    ##    date       stepsMean stepsMedian
    ##    <date>         <dbl>       <dbl>
    ##  1 2012-10-01   NaN              NA
    ##  2 2012-10-02     0.438           0
    ##  3 2012-10-03    39.4             0
    ##  4 2012-10-04    42.1             0
    ##  5 2012-10-05    46.2             0
    ##  6 2012-10-06    53.5             0
    ##  7 2012-10-07    38.2             0
    ##  8 2012-10-08   NaN              NA
    ##  9 2012-10-09    44.5             0
    ## 10 2012-10-10    34.4             0
    ## # … with 51 more rows

5- Time series plot of the average number of steps taken

    intervalsWithMainSteps <- summarize(group_by(myData,interval),stepsMean = mean(steps,na.rm =  TRUE))

    ## `summarise()` ungrouping output (override with `.groups` argument)

    plot( intervalsWithMainSteps$stepsMean ~ intervalsWithMainSteps$interval,type="l")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

6-The 5-minute interval that, on average, contains the maximum number of
steps between 500 and 1000

7-A Code to describe and show a strategy for imputing missing data

    numberOfNaSteps = sum(is.na(myData$steps))
    propOfNaSteps = numberOfNaSteps / length(myData$steps)

The total number of missing values in the dataset is 2304 of 17568 and
its prop is 0.1311475

7-B,7-C

I fill the missing values with the mean for that 5-minute interval.

    #loop through all rows
      for ( i in 1:nrow(myData)) {
        #check if steps variable is na
        if(is.na(myData[i,][1])) {
          
          #fill the steps variable with its corresponding mean interval
           myData[i,][1] = intervalsWithMainSteps$stepsMean[which(intervalsWithMainSteps$interval == myData[i,][3]$interval)] 
        }
      }

7-D Make a histogram of the total number of steps taken each day

    daysWithTotalStepsOfNewDataSet <- summarize(group_by(myData,date),stepsTotal = sum(steps))

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ggplot(daysWithTotalStepsOfNewDataSet, aes(date, stepsTotal)) + geom_bar(stat="identity")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-8-1.png)

7-E Calculate and report the mean and median total number of steps taken
per day

    daysWithMainAndMEdianStepsOfNewDataSet <- summarize(group_by(myData,date),stepsMean = mean(steps),stepsMedian = median(steps))

    ## `summarise()` ungrouping output (override with `.groups` argument)

    daysWithMainAndMEdianStepsOfNewDataSet

    ## # A tibble: 61 x 3
    ##    date       stepsMean stepsMedian
    ##    <date>         <dbl>       <dbl>
    ##  1 2012-10-01    37.4          34.1
    ##  2 2012-10-02     0.438         0  
    ##  3 2012-10-03    39.4           0  
    ##  4 2012-10-04    42.1           0  
    ##  5 2012-10-05    46.2           0  
    ##  6 2012-10-06    53.5           0  
    ##  7 2012-10-07    38.2           0  
    ##  8 2012-10-08    37.4          34.1
    ##  9 2012-10-09    44.5           0  
    ## 10 2012-10-10    34.4           0  
    ## # … with 51 more rows

7-F

Do these values differ from the estimates from the first part of the
assignment? What is the impact of imputing missing data on the estimates
of the total daily number of steps?

The weekends of the old dataset which have NA values, have been filled
with values.

8-A

Creating a new weedays column that contains weekday, weekend values.

    myData <- transform(myData , weekdays = ifelse(weekdays(date) %in% c("Saturday","Sunday"),"weekend","weekday"))
    myData$weekdays <-as.factor(myData$weekdays)

8-B

Make a panel plot containing a time series plot (i.e. type = “l”) of the
5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis)

    intervalsWithMainSteps <- summarize(group_by(myData,weekdays,interval),stepsMean = mean(steps))

    ## `summarise()` regrouping output by 'weekdays' (override with `.groups` argument)

    g<- ggplot(intervalsWithMainSteps, aes( intervalsWithMainSteps$interval, intervalsWithMainSteps$stepsMean)) 
    g + geom_line()  + facet_grid(weekdays ~ .)  +   xlab("Interval") + ylab("Number of steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-11-1.png)
