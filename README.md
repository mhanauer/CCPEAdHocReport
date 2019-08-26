---
title: "CCPEAdHocReport"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Load all releveant packages

```{r}
library(effects)
require(foreign)
library(plyr)
library(Amelia)
library(MASS)
library(psych)
library(ggplot2)
library(dplyr)      # for data manipulation
library(tidyr)      # for reshaping data
library(descr)
library(pscl)
library(lmtest)
library(countreg)
library(jtools)
library(descr)
library(prettyR)
```
Loading old data
```{r}
setwd("S:/Indiana Research & Evaluation/CCPE/Reports/AdHocReport")
CCPE_report = read.csv("gpraAdultAll,csv", header = TRUE, na.strings = c(-99, -98, 97))
#, CCPE_report$DAY.x, CCPE_report$YEAR.x
CCPE_report = CCPE_report[!is.na(CCPE_report$PARTID),]
sum(is.na(CCPE_report$PARTID))

```
Get the dates cleaned up so we can subset
```{r}
CCPE_report_date = data.frame(Month = CCPE_report$MONTH.x, Day = CCPE_report$DAY.x, Year = CCPE_report$YEAR.x)
sum(is.na(CCPE_report_date))
library(tidyr)
library(lubridate)
CCPE_report$date =  unite(CCPE_report_date, Date, sep = "/")
CCPE_report$date = unlist(CCPE_report$date)
library(naniar)
head(CCPE_report)
CCPE_report = replace_with_na(CCPE_report, replace = list(date = "NA/NA/NA"))
CCPE_report = CCPE_report[!is.na(CCPE_report$date),]
dim(CCPE_report)
CCPE_report$date = mdy(CCPE_report$date)
```
Grant years run from October 1st to September 30th
Example year 2 risk for cigarettes: RSKCIG
```{r}
CCPE_report_risk_cig_year_two = subset(CCPE_report, date >= "2016-10-01" & date < "2017-10-01")
dim(CCPE_report_risk_cig_year_two)
CCPE_report_risk_cig_year_two = data.frame(base = CCPE_report_risk_cig_year_two$RSKCIG.x, month3 = CCPE_report_risk_cig_year_two$RSKCIG.y)

## Need to R to recongize the NAs 
write.csv(CCPE_report_risk_cig_year_two, "CCPE_report_risk_cig_year_two.csv", row.names = FALSE)
CCPE_report_risk_cig_year_two = read.csv("CCPE_report_risk_cig_year_two.csv", header = TRUE, na.strings = "NA")
CCPE_report_risk_cig_year_two = na.omit(CCPE_report_risk_cig_year_two)

### Now percentage change
p_change = (CCPE_report_risk_cig_year_two$month3-CCPE_report_risk_cig_year_two$base) / CCPE_report_risk_cig_year_two$base

risk_cig_year_two_results = data.frame(base = mean(CCPE_report_risk_cig_year_two$base), month3 = mean(CCPE_report_risk_cig_year_two$month3), p_change = mean(p_change), n = dim(CCPE_report_risk_cig_year_two)[1])
risk_cig_year_two_results
```



