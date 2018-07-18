---
layout: post
title: Bigquery access ID-바꾸기
date: 2018-07-18 14:40
comments: true
external-url:
categories: R
---

Bigquery와 R을 연결하여 데이터를 분석하다 보면, 엑세스 문제로 재인증을 받아야 할 때가 종종 발생한다. 한국어로 된 해결책이 없어 영어 자료를 찾던 와중,  같은 방법을 이용하면 쉽게 해결할 수 있다는 걸 알았다. 

```R
getwd()
>"C:/Users/user/Documents"
library(bigrquery)
reset_access_cred()
```


1. R에서 getwd()를 통해 본인의 디렉토리를 확인
2. R에서 확인한 디렉토리에서 .httr-oauth 파일을 삭제
3. reset_access_cred()를 입력 후 재인증 팝업 화면이 뜨면 빅쿼리에 다시 로그인하기 
