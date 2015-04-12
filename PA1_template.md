---
title: "Reproducible Research Peer Review Project 1 - Analysis of Step Data"
author: "John Lovrinic"
date: "2015-04-08"
output: rmarkdown::html_vignette
vignette: >
  %\VignetteIndexEntry{Vignette Title}
  %\VignetteEngine{knitr::rmarkdown}
  \usepackage[utf8]{inputenc}
--- 

The purpose of this analysis is give the student the opportunity to gain practice with producing R Markdown documents in an acceptable form.  To that effect we are provided data on movement recorded from devices worn by unknown individuals who record these movements for a variety of reasons. 


**Data**

*from the assignment document *

>The data for this assignment can be downloaded from the course >web site:

>Dataset: 

>[Data for Course Peer Assessment 1] {https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip} [52K]

>The variables included in this dataset are:

>steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)

>date: The date on which the measurement was taken in YYYY-MM-DD format

>interval: Identifier for the 5-minute interval in which measurement was taken


>The dataset is stored in a comma-separated-value (CSV) file and >there are a total of 17,568 observations in >this dataset.

## Load and Process the Data

'''{r}
library(dplyr)
library(ggplot2)
raw.data<-read.cvs("activity.cvs")
summary(raw.data)
'''

## What is mean total number of steps taken per day?
