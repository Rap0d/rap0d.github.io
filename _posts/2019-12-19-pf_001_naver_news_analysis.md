---
layout: post
title: Naver 뉴스 댓글 유형 분석
subtitle: Naive Bayes
categories: portfolio
tags: portfolio
---

![r](/assets/img/logo/r-logo.png)

# Overview

네이버 뉴스의 댓글 유형을 R에서 제공하는 다양한 알고리즘으로 분석해 본다. 분석된 결과를 통해 뉴스 기사를 소비하는 유저들의 경향과 각 뉴스 세션 별 특징을 정리한다.

***

# Data set

네이버 뉴스 사이트에서 제공하는 일별 조회수 및 댓글수 TOP 30 자료를 크롤링했다.<br>가져오는 컬럼은 다음과 같다.

| 항목 | 비고 |
|:--------|:--------|
| 일간 뉴스 랭크 | 조회수와 댓글수 각각 나뉘어져 있음 |
| 제목 | |
| 부제목 | |
| 언론사 | |
| 게시일 | 기사 입력 시간 |
| 조회수 | |
| 댓글수 | |
| 현재 댓글수 | 총 댓글수 |
| 지운 댓글수 | 댓글 작성자가 지운 댓글 수 |
| 지워진 댓글수 | 규정 위반으로 인해 지워진 댓글 수 |
| 성별 비율 | |
| 연령대 비율 | 10대, 20대, 30대, 40대, 50대, 60대 이상으로 분류 |

***

# Crawling

## R Selenium

RSelenium 패키지를 사용하여 크롤링 하였다. 사용한 드라이버와 Selenium Standalone server는 아래에서 다운받아 사용하였다.
- [Selenium Standalone server](http://selenium-release.storage.googleapis.com/index.html)
  - 독립형 서버 실행 툴
- [Gecko Driver](https://github.com/mozilla/geckodriver/releases/tag/v0.17.0)
  - W3C WebDriver 호환 클라이언트를 사용하여 Gecko 기반 브라우저와 상호작용하기 위한 프록시
  - WebDriver 프로토콜의 HTTP API를 제공하여 Gecko 브라우저와 통신
  - 크롬(클라이언트)과 Selenium(서버) 사이의 중계(프록시) 역할
- [Chrome Driver](https://sites.google.com/a/chromium.org/chromedriver/)
  - 크롬 브라우저를 실행시키기 위한 드라이버

RSelenium에서 크롬 드라이버를 사용하기 위해 위 파일이 설치된 폴더에 접근하여 다음 명령어를 입력하여 gecko web driver를 이용해 selenium 서버를 4445 포트에서 실행시킨다.

```
java -Dwebdriver.gecko.driver="geckodriver.exe" -jar selenium-server-standalone-3.9.1.jar -port 4445
```

서버를 실행시키고, R Script에서 원격 드라이버를 연다.

```R
remDr <- remoteDriver(remoteServerAdd='localhost', port=4445L, browserName='chrome')
remDr$open()
```

Rselenium 패키지의 remoteDriver 함수의 매개변수에 remoteServer(localhost)와 위에서 넣은 포트(4445), browserName(chrome)을 넣어 open한다.

## Target Site

크롤링할 대상 사이트 url은 다음 url의 query를 변경하여 사용하였다.

```
https://news.naver.com/main/ranking/popularMemo.nhn?rankingType=popular_memo&sectionId=103&date=
```

## Target Data

크롤링할 뉴스 기사는 최근 1년치 데이터로, 2018년 11월 1일부터 2019년 10월 31일까지로 제한하였다.

이는 Training데이터 및 현황 분석에 사용되며, 추가로 Test Data를 위해 11월 1일부터 30일 데이터를 추가로 크롤링 하였다.

Target url에 접근하기 앞서 반복문이 끝나는 시점의 Flag 변수를 하나 두어, Flag가 True가 되면 반복문을 탈출하도록 하였다.

```R
if(finFlag == T) { break }
```

또한 시작과 종료 시점에 대한 예외처리를 하여 잘못된 데이터가 들어가지 않도록 하였다.

```R
if(as.integer(y) < as.integer(str_sub(startDate, 1, 4)) & isFALSE(startFlag)) { next }
```



Target url의 요소에 접근하려면 드라이버 객체의 `navigate()`함수를 사용한다.

