\#Introduction
==============

This is the first project of the course Reprodicible Research from the
Data Science Specialization.

Synopsis
--------

The purpose of this project was: - loading data and processing it -
handling missing values - interpreting data to answer reasearch
questions

Data
----

The data for this assignment was downloaded from the course web site:

Dataset: Activity monitoring data [52K] The variables included in this
dataset are:

-   steps: Number of steps taking in a 5-minute interval (missing values
    are coded as NA)

-   date: The date on which the measurement was taken in YYYY-MM-DD
    format

-   interval: Identifier for the 5-minute interval in which measurement
    was taken

The dataset is stored in a comma-separated-value (CSV) file and there
are a total of 17,568 observations in this dataset.

Loading Data
------------

Downloading the data and loading it into a variable:

    if(!file.exists("getdata-projectfiles-UCI HAR Dataset.zip")) {
            temp <- tempfile()
            download.file("http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",temp)
            unzip(temp)
            unlink(temp)
    }

    data <- read.csv("activity.csv")

What is the the mean total number of steps taken each day?
----------------------------------------------------------

Sum steps by day,create Histogram and calcualte mean and median:
![](ReprodicibleResearch_files/figure-markdown_strict/unnamed-chunk-2-1.png)<!-- -->
The mean is r rmean and the median is r rmedian \#\#What is he average
of the daily activity pattern? - Calculate the average steps per
interval each day - Plot the average no. of steps for each day by
intervals. - Find the interval with te highest average no. of steps.
![](ReprodicibleResearch_files/figure-markdown_strict/unnamed-chunk-3-1.png)<!-- -->
The 5-minute interval, on average across all the days in the data set,
containing the maximum number of steps is 835.

Impute missing values. Compare between imputed and non-imputed data
-------------------------------------------------------------------

Missing data needed to be imputed. Only a simple imputation approach was
required for this assignment. Missing values were imputed by inserting
the average for each interval. Thus, if interval 10 was missing on
10-02-2012, the average for that interval for all days (0.1320755),
replaced the NA.

    incomplete <- sum(!complete.cases(data))
    imputed_data <- transform(data, steps = ifelse(is.na(data$steps), steps_by_interval$steps[match(data$interval, steps_by_interval$interval)], data$steps))

Zeroes were imputed for 10-01-2012 because it was the first day and
would have been over 9,000 steps higher than the following day, which
had only 126 steps. NAs then were assumed to be zeros to fit the rising
trend of the data.

    imputed_data[as.character(imputed_data$date) == "2012-10-01", 1] <- 0

Recount total steps and create a histogram

    steps_by_day_i <- aggregate(steps ~ date, imputed_data, sum)
    hist(steps_by_day_i$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")

    #Create Histogram to show difference. 
    hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="red", xlab="Number of Steps", add=T)
    legend("topright", c("Imputed", "Non-imputed"), col=c("blue", "red"), lwd=10)

![](ReprodicibleResearch_files/figure-markdown_strict/unnamed-chunk-6-1.png)<!-- -->

Calculate new mean and median for imputed data.

Calculate the difference between imputed and non-impputed data.

Calculate the total difference

-   The imputed data mean is 1.058969410^{4}
-   The imputed data median is 1.076618910^{4}
-   The difference between the non-imputed mean and imputed mean
    is -176.4948964
-   The difference between the non-imputed mean and imputed mean is
    1.1886792
-   The difference between total number of steps between imputed and
    non-imputed data is 7.536332110^{4}. Therefore there a total of
    7.536332110^{4} more steps.

Difference in activity pattern on weekdays and weekends?
--------------------------------------------------------

Created a plot to compare and contrast number of steps between the week
and weekend. There is a higher peak earlier on weekdays, and more
overall activity on weekends.

    weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", 
                  "Friday")
    imputed_data$dow = as.factor(ifelse(is.element(weekdays(as.Date(imputed_data$date)),weekdays), "Weekday", "Weekend"))

    steps_by_interval_i <- aggregate(steps ~ interval + dow, imputed_data, mean)

    library(lattice)

    xyplot(steps_by_interval_i$steps ~ steps_by_interval_i$interval|steps_by_interval_i$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")

![](ReprodicibleResearch_files/figure-markdown_strict/unnamed-chunk-10-1.png)<!-- -->
