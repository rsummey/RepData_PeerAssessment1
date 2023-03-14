RepResearch1
================
2023-03-14

# Data analysis

This is an R Markdown document reporting an analysis of biometric data.
It will contain: Commit containing full submission Code for reading in
the dataset and/or processing the data Histogram of the total number of
steps taken each day Mean and median number of steps taken each day Time
series plot of the average number of steps taken The 5-minute interval
that, on average, contains the maximum number of steps Code to describe
and show a strategy for imputing missing data Histogram of the total
number of steps taken each day after missing values are imputed Panel
plot comparing the average number of steps taken per 5-minute interval
across weekdays and weekends All of the R code needed to reproduce the
results (numbers, plots, etc.) in the report

``` obtaining
### reproducible research project1

URL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(URL, "represearch")
unzip("represearch")
setwd("~/Box/DataScienceCourse/represearch")
activity <- read.csv("activity.csv")
```

## Including Plots

You can also embed plots, for example:

``` total
df1 <- tapply(activity$steps, factor(activity$date), 
              sum, na.rm = TRUE)
df1 <- as.data.frame(df1)
colnames(df1) <- "total_steps"
p1 <- ggplot(df1) +
  geom_bar(aes(x = rownames(df1), y = total_steps), stat = "identity")
p1
```

``` mean
mean(df1$total_steps, na.rm = TRUE)
median(df1$total_steps, na.rm = TRUE)
```

## time series

``` time
df2 <- tapply(activity$steps, factor(activity$interval), 
              mean, na.rm = TRUE)
df2 <- as.data.frame(df2)
colnames(df2) <- "steps"
p2 <- ggplot(df2) + 
  geom_point(aes(x = rownames(df2), y = steps))
p2
```

## time of day with maximum steps

``` max
rownames(df2)[max(df2$steps)]
```

## missing values

### determining how many

``` r
activity <- read.csv("~/Box/DataScienceCourse/represearch/activity.csv")
table(is.na(activity$steps))
```

    ## 
    ## FALSE  TRUE 
    ## 15264  2304

So 2304 measurements of 17568 are NA.

### imputing averages in for that time of day

``` r
df2 <- tapply(activity$steps, factor(activity$interval), 
              mean, na.rm = TRUE)
missing <- which(is.na(activity))
activitymissing <- activity[missing,]
head(activitymissing)
```

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

``` r
df2$interval = rownames(df2)
```

    ## Warning in df2$interval = rownames(df2): Coercing LHS to a list

``` r
activitymissing <- merge(activitymissing, df2, by =
                                               "interval")
activity<- na.omit(activity)
activitymissing <- activitymissing[,c(1, 3:4)]
colnames(activitymissing) <- c("interval", "date", "steps")
activity <- rbind(activity, activitymissing)
```

### check graphs after imputing data

``` r
df3 <- tapply(activity$steps, factor(activity$date), 
              sum, na.rm = TRUE)
df3 <- as.data.frame(df3)
colnames(df3) <- "total_steps"
plot <- ggplot(df3) +
  geom_bar(aes(x = rownames(df3), y = total_steps), stat = "identity")
plot
```

![](PA1_template_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
mean(df3$total_steps, na.rm = TRUE)
```

    ## [1] 9419.081

``` r
median(df3$total_steps, na.rm = TRUE)
```

    ## [1] 10395

``` r
df4 <- tapply(activity$steps, factor(activity$interval), 
              mean, na.rm = TRUE)
df4 <- as.data.frame(df4)
colnames(df4) <- "steps"
p4 <- ggplot(df4) + 
  geom_point(aes(x = rownames(df4), y = steps))
p4
```

![](PA1_template_files/figure-gfm/unnamed-chunk-3-2.png)<!-- --> Mean
and median are now equal to each other after replacing average interval
data; both higher now than before

## now look at this by weekend vs weekday

``` r
activity$date <- as.Date(activity$date)
activity$weekday <- weekdays(activity$date)
activity$weekday <- replace(activity$weekday, activity$weekday %in%
                              c("Monday", "Tuesday", "Wednesday",
                                "Thursday", "Friday"),
                            c("Weekday"))
activity$weekday <- replace(activity$weekday, activity$weekday %in%
                              c("Saturday", "Sunday"),
                            c("Weekend"))


p6 <- xyplot(steps ~ interval | weekday,
       data = activity,
       type = "l")
p6
```

![](PA1_template_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.
