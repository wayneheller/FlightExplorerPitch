---
title       : Flight Explorer Pitch
subtitle    : Developing Data Products Final Project
author      : Wayne Heller
job         : 7/23/2017
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
github      :
  user: wayneheller
  repo: FlightExplorerPitch
---


## Introduction

### Wouldn't Be Great If...

You knew whether your flight was going to be cancelled or delayed BEFORE you booked it?

Flight Explorer is a new application that allows you to see the past arrival delays and cancellations of your flight!

---

## Using Transit Bureau Data

Behind and intuitive user interface, Flight Explorer uses transportation bureau data from the prior 12 months to give you the information you need to have delay free travel.

![image](FlightExplorer.PNG)

---

## Summarized Flight Data
Individual Files from the Transportation Bureau were consolidated and summarized for use in the application

```r
library(dplyr)

dfFlightData <- NULL
for(i in 1:13){
        f.path <- file.path(getwd(), paste0("902002334_T_ONTIME "
                ,"(", as.character(i),")"), "902002334_T_ONTIME.csv")
        dfFlights <- read.csv(f.path)
        if(is.data.frame(dfFlightData)){
                dfFlightData <- rbind(dfFlights, dfFlightData)
        }
        else { dfFlightData <- dfFlights }
}
```

---

### Summarized Flight Data (Continued)

```r
dfAirlines <- read.csv("L_AIRLINE_ID.csv")
names(dfAirlines) <- c("AIRLINE_ID", "AIRLINE_NAME")

dfFlightData <- merge(dfFlightData, dfAirlines, by.x = "AIRLINE_ID", 
                      by.y = "AIRLINE_ID")
dfFlightData <- dfFlightData %>% select(MONTH, DAY_OF_WEEK, AIRLINE_NAME, 
                FL_NUM, ORIGIN_CITY_NAME, DEST_CITY_NAME, ARR_DELAY_NEW, CANCELLED)
dfFlightSummary <- dfFlightData %>% group_by(MONTH, DAY_OF_WEEK, AIRLINE_NAME, 
                FL_NUM, ORIGIN_CITY_NAME, DEST_CITY_NAME)
dfFlightSummary <- summarize(dfFlightSummary, 
                             avg_delay = round(mean(ARR_DELAY_NEW, na.rm = TRUE),0), 
                             pct_cancelled = round(100 * mean(CANCELLED, na.rm = TRUE),0))
write.csv(dfFlightSummary, "FlightSummaryData.csv", row.names = FALSE)
```


---

## May All Your Flights be Delay-Free

Shiny Application https://rougeone.shinyapps.io/FlightExplorer

Code Repo:  
https://github.com/wayneheller/developingdataproducts/tree/gh-pages/final_project/FlightExplorer

Pitch Deck https://wayneheller.github.io/FlightExplorerPitch

Flight Data Source https://www.transtats.bts.gov/Fields.asp

