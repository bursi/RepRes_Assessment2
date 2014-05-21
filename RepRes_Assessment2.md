Analysis of storm damages across the US
========================================================


## Synopsis


## Data Processing

First, we read the data into R:


```r
rawdata <- read.csv(file = "repdata_data_StormData.csv", header = TRUE, skip = 0)
```


Sum the fatalities and injuries for all event types:


```r
fatalities <- aggregate(FATALITIES ~ EVTYPE, FUN = sum, data = rawdata)
injuries <- aggregate(INJURIES ~ EVTYPE, FUN = sum, data = rawdata)
```


Define a function to convert PROPDMGEXP and CROPDMGEXP to numeric values


```r
exp2num <- function(exponent) {
    ifelse(exponent == "K", 1000, ifelse(exponent == "M", 1e+06, ifelse(exponent == 
        "B", 1e+09, 1)))
}
```


Sum the property damage and crop damage for all event types:


```r
rawdata$PROPERTYDMG <- rawdata$PROPDMG * exp2num(rawdata$PROPDMGEXP)
rawdata$CROPDAMAGE <- rawdata$CROPDMG * exp2num(rawdata$CROPDMGEXP)
propertydamage <- aggregate(PROPERTYDMG ~ EVTYPE, FUN = sum, data = rawdata)
cropdamage <- aggregate(CROPDAMAGE ~ EVTYPE, FUN = sum, data = rawdata)
```


Calculate the total damage (property plus crop) for all event types:


```r
totaldamage <- propertydamage
totaldamage$PROPERTYDMG <- NULL
totaldamage$DAMAGE <- propertydamage$PROPERTYDMG + cropdamage$CROPDAMAGE
```


Extract the event types with nonzero fatalities / injuries / total damage and order them in decreasing order:


```r
fatal <- fatalities[fatalities$FATALITIES > 0, ]
fatal <- fatal[order(fatal$FATALITIES, decreasing = TRUE), ]
inj <- injuries[injuries$INJURIES > 0, ]
inj <- inj[order(inj$INJURIES, decreasing = TRUE), ]
damage <- totaldamage[totaldamage$DAMAGE > 0, ]
damage <- damage[order(damage$DAMAGE, decreasing = TRUE), ]
```



## Results


```r
par(mar = c(8, 4.1, 4.1, 2.1), cex.axis = 0.7)
plot(damage$DAMAGE[1:10]/1e+09, xaxt = "n", xlab = "", ylab = "total damage (in billion USD)", 
    pch = 16, main = "total damage per event type")
axis(1, at = 1:10, labels = damage$EVTYPE[1:10], las = 2)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 





```r
par(mar = c(8, 4.1, 4.1, 2.1), cex.axis = 0.7)
plot(fatal$FATALITIES[1:10]/1000, xaxt = "n", xlab = "", ylab = "fatalities (in thousands)", 
    pch = 16, main = "total fatalities per event type")
axis(1, at = 1:10, labels = damage$EVTYPE[1:10], las = 2)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 











