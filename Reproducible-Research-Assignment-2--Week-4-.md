---
title: "Reproducible Research Assignment 2 (Week 4)"
author: "Barbara Rattner"
date: "10/18/2019"
output: 
  html_document: 
    keep_md: yes
---



##(Title) 

#Synopsis



```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
download.file(url, "StormData.csv")
StormData <- read.csv("StormData.csv")
```


