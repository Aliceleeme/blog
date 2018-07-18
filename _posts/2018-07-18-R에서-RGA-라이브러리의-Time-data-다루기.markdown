---
layout: post
title: R에서 RGA 라이브러리의 Time data 다루기
date: 2018-07-18 18:00
comments: true
external-url:
categories: R
---

R과 google analytics를 연결하여 분석을 진행하기 위해서는 time data를 정제하고 형식을 통일해야만 한다. 하지만 R의 경우 time data를 다루기 어려운 것으로 유명한데, RGA 라이브러리에서 시간 데이터를 다룰 때 어떻게 작업하였는지 정리한 코드를 공개하고 이를 설명하고자 한다. 

아래의 코드는 베이지안 분석에서 사용되는 time-series 분석 중 하나인 Causal impact 그래프를 그리기 위해 사용되었다. 

```R
install.packages("RGA")

library(RGA)
authorize()      #RGA 접속 인증작업 
rga.open(instance == "ga")

library(CausalImpact)      #Time-series 그래프를 그리기 위한 라이브러리 

# GA 내의 profile list 확인
ga_profiles = list_profiles()
id = ga_profiles[grep("web_site_url", ga_profiles$websiteUrl), "id"]
list_profiles()      #GA 안에 있는 profiles ID 및 정보 확인 가능 

viewID <- "ID_number"     #보고자하는 viewID 설정 
workingData <- get_ga(viewID, start.date = as.Date("2017-11-01"), 
end.date = as.Date("2018-04-30"), metrics = "ga:sessions", dimensions = "ga:date") 

workingData <- zoo(workingData[, -1], workingData$date)       #시간 데이터 변환 

pre.period = c(as.POSIXct("2017-11-01"), as.POSIXct("2017-12-31"))   #as.Date to as.POSIXct
post.period = c(as.POSIXct("2018-03-01"), as.POSIXct("2018-04-30"))

im <- CausalImpact(workingData, pre.period, post.period)
plot(im)

```
