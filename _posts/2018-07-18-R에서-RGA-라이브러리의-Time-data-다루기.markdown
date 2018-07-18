---
layout: post
title: R에서 RGA 라이브러리의 Time data 다루기
date: 2018-07-18 18:00
comments: true
external-url:
categories: R
---

R과 google analytics를 연결하여 분석을 진행하기 위해서는 time data를 정제하고 형식을 통일해야만 한다. 하지만 R의 경우 time data를 다루기 어려운 것으로 유명한데, [RGA](https://cran.r-project.org/web/packages/RGA/README.html) 라이브러리에서 시간 데이터를 다룰 때 어떻게 작업하였는지 정리한 코드를 공개하고 이를 설명하고자 한다. 

아래의 코드는 베이지안 분석에서 사용되는 time-series 분석 중 하나인 Causal impact 그래프를 그리기 위해 사용한 내용이다. 

```R
install.packages("RGA")
install.packages("CausalImpact")

library(CausalImpact)      #Time-series 그래프를 그리기 위한 라이브러리 
library(RGA)
authorize()      #RGA 접속 인증작업 
rga.open(instance == "ga")

## GA 내의 profile list 확인
ga_profiles = list_profiles()
id = ga_profiles[grep("web_site_url", ga_profiles$websiteUrl), "id"]
list_profiles()      #GA 안에 있는 profiles ID 및 정보 확인 가능 

viewID <- "ID_number"     #보고자하는 GA의 viewID 설정 
workingData <- get_ga(viewID, start.date = as.Date("2017-11-01"), 
end.date = as.Date("2018-04-30"), metrics = "ga:sessions", dimensions = "ga:date") 

workingData <- zoo(workingData[, -1], workingData$date)       #시간 데이터 변환 

pre.period = c(as.POSIXct("2017-11-01"), as.POSIXct("2017-12-31"))   #as.Date to as.POSIXct
post.period = c(as.POSIXct("2018-03-01"), as.POSIXct("2018-04-30"))

im <- CausalImpact(workingData, pre.period, post.period)
plot(im)   #plot을 확인하기 위해서 필수적으로 입력해야 한다 

```

보통 RGA 라이브러리로 time-series 그래프를 그리기 위해 데이터를 정제하다보면 시간 데이터 타입 때문에 그래프가 그려지지 않는 경우가 많다.
이럴 때 zoo()와 as.POSIXct를 이용하여 시간 데이터의 타입을 하나로 통일해주면 오류 없이 그래프가 그려진다.

위의 코드의 경우 최초에 as.Date로 날짜 데이터를 받았는데 CausalImpact 함수에서 계속 오류가 나서 zoo와 as.POSIXct를 통해 오류를 해결하였다.
그리고 두 가지 클러스터를 비교하기 위한 목적의 그래프가 아니었으므로, 데이터를 pre.period의 from과 post.period의 to로 받아서 데이터를 다운받아 그래프를 그리기 직전에 pre와 post를 구분하였다. 
