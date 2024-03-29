---
title: "Reproducible Research Assignment 2 (Week 4)"
author: "Barbara Rattner"
date: "10/18/2019"
output: 
  html_document: 
    keep_md: yes
---



## **The effect of storm events on population health and econonmy of the United States (1950-2011)** 

# **Synopsis**

I have explored the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States from 1950 to 2011, including when and where they occurred, as well as estimates of any fatalities, injuries, and property damage. The goals of this study were to determine (1) which types of events are most harmful to health population and; (2) which types of events have the greatest economic consequences. My analysis indicates that tornadoes are one of the events causing the most fatalities and injuries and that flooding have caused the largest economic impact. 

# **Data Analysis**

**Data Loading**

Data for this analysis were loaded from the url  https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2. These data are from the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database and track events from 1950 to 2011. The National Weather Service Storm Data Documentation can be found [here](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf).


```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
download.file(url, "StormDataRaw.csv")
StormDataRaw <- read.csv("StormDataRaw.csv")
```

I took a look at the dimensions of the data:


```r
dim(StormDataRaw)
```

```
## [1] 902297     37
```

I looked at the variables reported:


```r
names(StormDataRaw)
```

```
##  [1] "STATE__"    "BGN_DATE"   "BGN_TIME"   "TIME_ZONE"  "COUNTY"    
##  [6] "COUNTYNAME" "STATE"      "EVTYPE"     "BGN_RANGE"  "BGN_AZI"   
## [11] "BGN_LOCATI" "END_DATE"   "END_TIME"   "COUNTY_END" "COUNTYENDN"
## [16] "END_RANGE"  "END_AZI"    "END_LOCATI" "LENGTH"     "WIDTH"     
## [21] "F"          "MAG"        "FATALITIES" "INJURIES"   "PROPDMG"   
## [26] "PROPDMGEXP" "CROPDMG"    "CROPDMGEXP" "WFO"        "STATEOFFIC"
## [31] "ZONENAMES"  "LATITUDE"   "LONGITUDE"  "LATITUDE_E" "LONGITUDE_"
## [36] "REMARKS"    "REFNUM"
```

**Data Processing**


I will only consider the variables for Type of Event (EVTYPE), Number of Fatalities (FATALITIES), Number of Injures (INJURIES), Property Damage (PROPDMG and PROPDMGEXP),  and Crop Damage  (CROPDMG and CROPDMGEXP).

Therefore, a new table including only those variables was generated:


```r
variables <- c("EVTYPE","FATALITIES","INJURIES","PROPDMG","PROPDMGEXP","CROPDMG","CROPDMGEXP")
StormData <- StormDataRaw[variables]
```

 
To evaluate the economic consequences of events, I will look at property and crop damage (PROPDMG and CROPDMG).The magnitude of the damage caused by the events is presented in the variables PROPDMGEXP and CROPDMGEXP. The variables ‘PROPDMGEXP’ and ‘CROPDMGEXP’ contain codes that represent the exponents of the ‘PROPDMG’ & ‘CROPDMG’ variables.


```r
unique(StormData$PROPDMGEXP)
```

```
##  [1] K M   B m + 0 5 6 ? 4 2 3 h 7 H - 1 8
## Levels:  - ? + 0 1 2 3 4 5 6 7 8 B h H K m M
```


```r
unique(StormData$CROPDMGEXP)
```

```
## [1]   M K m B ? 0 k 2
## Levels:  ? 0 2 B k K m M
```

where “K” and "k" = thousands; “M” and "m"= millions and; “B” and "b" = billions.

The following incorporates the magnitude of the damage to dollar value of the damage.


```r
StormData[StormData$PROPDMGEXP == "K", ]$PROPDMG <- StormData[StormData$PROPDMGEXP == "K", ]$PROPDMG * 1000
StormData[StormData$PROPDMGEXP == "M", ]$PROPDMG <- StormData[StormData$PROPDMGEXP == "M", ]$PROPDMG * 1e+06
StormData[StormData$PROPDMGEXP == "m", ]$PROPDMG <- StormData[StormData$PROPDMGEXP == "m", ]$PROPDMG * 1e+06
StormData[StormData$PROPDMGEXP == "B", ]$PROPDMG <- StormData[StormData$PROPDMGEXP == "B", ]$PROPDMG * 1e+09
StormData[StormData$CROPDMGEXP == "K", ]$CROPDMG  <- StormData[StormData$CROPDMGEXP  == "K", ]$CROPDMG  * 1000
StormData[StormData$CROPDMGEXP  == "M", ]$CROPDMG  <- StormData[StormData$CROPDMGEXP  == "M", ]$CROPDMG  * 1e+06
StormData[StormData$CROPDMGEXP  == "m", ]$CROPDMG  <- StormData[StormData$CROPDMGEXP  == "m", ]$CROPDMG  * 1e+06
StormData[StormData$CROPDMGEXP == "B", ]$CROPDMG  <- StormData[StormData$CROPDMGEXP  == "B", ]$CROPDMG  * 1e+09
StormData[StormData$CROPDMGEXP == "k", ]$CROPDMG  <- StormData[StormData$CROPDMGEXP  == "k", ]$CROPDMG  * 1000
```

# Results

**Across the United States, which types of events (as indicated in the EVTYPE variable) are most harmful with respect to population health?**
 
To select the 10 events that are the most harmful to the population health, I used the following code:


```r
FatalEvents <- aggregate(FATALITIES ~ EVTYPE, data = StormData, FUN = sum)
InjuryEvents <- aggregate(INJURIES ~ EVTYPE, data = StormData, FUN = sum)
Top10FatalEvents <- FatalEvents[order(-FatalEvents$FATALITIES),][1:10,]
Top10InjuryEvents <- InjuryEvents[order(-InjuryEvents$INJURIES),][1:10,]
```

The 10 events causing the most deaths and most injuries between 1950 and 2011 can be seen in Fig. 1.
 

```r
 par(mfrow = c(1,2), mar = c(12,4,3,2), mgp = c(3,1,0), las=3, cex= 0.8)
 barplot(Top10FatalEvents$FATALITIES, names.arg= Top10FatalEvents$EVTYPE, ylim= c(0,8000),col="orange",ylab="Number of Fatalities", main=" A. Top 10 Events Causing the Most Fatalities")
 barplot(Top10InjuryEvents$INJURIES, names.arg=Top10InjuryEvents$EVTYPE,ylim= c(0,90000), col="orange", ylab="Number of Injuries", main=" B. Top 10 Events Causing the Most Injuries")
```

![](Reproducible-Research-Assignment-2--Week-4-_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
 
**Figure 1.**The top 10 events causing the most deaths and injuries between 1050 and 2011 in the United States. **(A)** Barplot of the number of fatalities caused by each the ten events that caused the most deaths between 1950 and 2011. **(B)** Barplot of the of the number of injuries caused by each the ten events that caused the most injuries between 1950 and 2011. 
 
 **Across the United States, which types of events have the greatest economic consequences?**

The most Property and Crop damage caused by the different events can be identified with the following code:


```r
PropertyDamage <- aggregate(PROPDMG ~ EVTYPE, data = StormData,FUN = sum)
CropDamage <- aggregate(CROPDMG ~EVTYPE, data = StormData, FUN = sum)
PropertyDamage1 <- PropertyDamage[PropertyDamage$PROPDMG >0,]
CropDamage1 <- CropDamage[CropDamage$CROPDMG >0,]
OrderedPropertyDamage <- PropertyDamage1[order(PropertyDamage1$PROPDMG, decreasing = TRUE),]
Top10PropertyDamage <- OrderedPropertyDamage[1:10,]
OrderedCropDamage <- CropDamage1[order(CropDamage1$CROPDMG, decreasing = TRUE),]
Top10CropDamage <- OrderedCropDamage[1:10,]
```

The 10 events causing the most damage to property and crops between 1950 and 2011 can be seen in Fig.2


```r
par(mfrow = c(1, 2), mar = c(12, 4, 3, 2), mgp = c(3, 1, 0), las=3,cex = 0.8, cex.main = 0.9)
barplot((Top10CropDamage$CROPDMG), names.arg = Top10CropDamage$EVTYPE, col="orange", ylab=" Cost of Property Damage (in dollars)", main="Top 10 Events Causing Highest Property Damage")
barplot((Top10PropertyDamage$PROPDMG), names.arg = Top10PropertyDamage$EVTYPE, col="orange", ylab=" Cost of Crop Damage (in dollars)", main="Top 10 Events Causing Highest Crop Damage")
```

![](Reproducible-Research-Assignment-2--Week-4-_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

**Figure 2.** The 10 events causing the most damage to property and crops between 1950 and 2011 in the United States. **(A)** Barplot of the property damage caused by each the ten events that caused the most property damage between 1950 and 2011. **(B)** Barplot of the of the crop damage caused by each the ten events that caused the most crop damage between 1950 and 2011.

# Conclusions

In the United States, from 1950 to 2011, tornadoes affected human health the most, causing the most deaths and injuries. When looking at property and crop damage, flooding appears to be one of the events that caused the highest damage in both. Drought has also caused the most damage to property. 
