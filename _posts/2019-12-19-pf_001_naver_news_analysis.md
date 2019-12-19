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

RSelenium 패키지를 사용하여 크롤링 하였다. Selenium을 사용하는 이유는 GET이나 POST로 가져오기 힘든 경우 사용하면 편리하다. 예를 들어 클릭해서 로그인 후 내용을 크롤링 하거나, 검색어를 입력해서 크롤링 하는 경우, 웹표준을 지키지 않아서 크롤링이 어려운 경우 등에 사용한다.

사용한 드라이버와 Selenium Standalone server는 아래에서 다운받아 사용하였다.
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

- sectionId의 범위는 100부터 105까지 총 6개 Section(정치, 경제, 사회, 문화, 생활, IT)이다.
- date의 형식은 20190101과 같은 8자리 숫자열을 입력한다. 

## Target Data

크롤링할 뉴스 기사는 최근 1년치 데이터로, 2018년 11월 1일부터 2019년 10월 31일까지로 제한하였다. 이는 Training데이터 및 현황 분석에 사용되며, 추가로 Test Data를 위해 11월 1일부터 30일 데이터를 추가로 크롤링 하였다.

Target url에 접근하기 앞서 반복문이 끝나는 시점의 Flag 변수를 하나 두어, Flag가 True가 되면 반복문을 탈출하도록 하였다.

```R
if(finFlag == T) { break }
```

또한 시작과 종료 시점에 대한 예외처리를 하여 잘못된 데이터가 들어가지 않도록 하였다.

```R
if(as.integer(y) < as.integer(str_sub(startDate, 1, 4)) & isFALSE(startFlag)) { next }
```

date 변수에 들어갈 날짜를 만들기 위해 다음 코드를 통해 날짜 범위를 만들었다.

```R
# Disgard months with no 31st (except February)
if(m %in% c('04','06','09','11') & d == 31) { next }
# Disgard February (special cases)
if(m %in% c('02') & d >= 30) { next }
```

## Rvest를 사용한 Crawling

일반적인 파싱을 할 수 있는 Rvest 패키지의 함수를 사용하여 기사의 제목과 부제, 언론사 그리고 조회수를 파싱했다.

댓글에 관한 요소들과 통계는 Rvest 패키지로 접근이 불가능하여 Rselenium을 사용하였다.

위에서 만들어진 날짜들을 Target URL에 넣기 위해 date 변수를 만들어 url에 paste한다. 그리고 url이 정상적인 데이터인건지 검증하는 단계를 거친다.

```R
dat <- paste(y, m, d, sep='')
if(dat == stopDate) { finFlag = T }
url <- paste(url, dat, sep='')

html <- chkURL(url)
# Checks URL
while(is.list(html) == F) {
    html <- chkURL(url)
}
```

이 때, `chkURL()` 함수를 통해 verify된 url만 rvest 패키지의 read_html()로 html 변수에 반환한다.

이 때, html 변수에는 Target url의 파싱된 데이터가 text로 들어가게 된다.

```R
chkURL <- function(url) {
    out <- tryCatch(
        {
            html <- read_html(url)
            return(html)
        },
        error=function(cond) {
            return(F)
        },
        warning=function(cond) {
            return(F)
        },
        finally={
            cat('chkURL function called\n')
        }
    )
}
```

파싱된 html의 요소에 접근하려면 `html_nodes()` 함수를 이용하여 클래스와 id에 접근한다.

이 때, 클래스는 `.`, id는 `#`을 사용하여 접근할 수 있다.

다음은 제목, 부제목, 언론사, 조회수를 가져오는 소스코드이다.

```R
list <- html %>% html_nodes('.ranking_list') %>% html_nodes('.ranking_text')
title <- list %>% html_nodes('.ranking_headline') %>% html_nodes('a') %>% html_text()
subti <- list %>% html_nodes('.ranking_lede') %>% html_text()
source <- list %>% html_nodes('.ranking_office') %>% html_text()
view <- list %>% html_nodes('.ranking_view') %>% html_text()
```

만약 title(제목)의 길이가 0이면 크롤링이 되지 않았다는 경우로 for문을 건너뛴다.

```R
if(len == 0) { next }
```

## R Selenium을 사용한 Crawling

Selenium을 사용하기 위해 기존 url에서 oid, aid 부분을 가져온다. 여기서 oid는 언론사, aid는 언론사 id이다. 

```R
url <- 'https://news.naver.com/main/ranking/read.nhn?'
u <- urls[i]
oid <- u %>% str_extract('oid=[0-9]+') %>% str_sub(5)
aid <- u %>% str_extract('aid=[0-9]+') %>% str_sub(5)
url <- paste(url, 'm_view=1&rankingType=popular_day&oid=', oid,'&aid=', aid, '&date=', dat, '&type=1&rankingSectionId=102&rankingSeq=', i, sep='')
```

언론사 댓글에 들어가기 위해 드라이버 객체의 `navigate()`함수를 사용한다. 이 함수의 매개변수에 url을 넣어 드라이버 객체가 해당 url에 접근하도록 한다.

```R
remDr$navigate(url)
```

Selenium에서는 html 요소를 찾기 위해 `findElements()`함수를 사용한다. 

```R
elm <- remDr$findElements('class','u_cbox_info_txt')
```

반환된 요소들의 공백을 제거하기 위해 trim과 공백 문자들을 제거하는 함수를 사용하여 전처리를 한다. 이 때, 반환된 요소들은 list로 저장되어 `elm[[1]]`로 첫 번째 요소에 접근한다. 그리고 네이버 정책상 댓글수가 100개 미만인 기사들은 댓글 작성자의 통계를 제공하지 않으므로 결측치로 들어오게 된다. 결측치가 있다면 결측치를 처리하고 반복문을 탈출한다.

```R
currCmt <- cleanc(c(currCmt, as.character(elm[[1]]$getElementText())))

if(is.na(as.integer(currCmt[i]))) {
currCmt[i] = NA
deleted <- c(deleted, NA)
brokenPolicy <- c(brokenPolicy, NA)
maleRatio <- c(maleRatio, NA)
femaleRatio <- c(femaleRatio, NA)
X10 <- c(X10, NA)
X20 <- c(X20, NA)
X30 <- c(X30, NA)
X40 <- c(X40, NA)
X50 <- c(X50, NA)
X60 <- c(X60, NA)
print("No Data - appended NA values")
next()
}
```

만약 댓글 통계량이 있다면 해당하는 변수에 반환하고, 모든 변수들을 data frame으로 묶어준다.

```R
maleRatio <- c(maleRatio, as.character(elm[[1]]$getElementText()))
femaleRatio <- c(femaleRatio, as.character(elm[[2]]$getElementText()))
X10 <- c(X10, as.character(elm[[3]]$getElementText()))
X20 <- c(X20, as.character(elm[[4]]$getElementText()))
X30 <- c(X30, as.character(elm[[5]]$getElementText()))
X40 <- c(X40, as.character(elm[[6]]$getElementText()))
X50 <- c(X50, as.character(elm[[7]]$getElementText()))
X60 <- c(X60, as.character(elm[[8]]$getElementText()))

infoDF <- data.frame(currCmt=currCmt, deleted=deleted, brokenPolicy=brokenPolicy, maleRatio=maleRatio, femaleRatio=femaleRatio, X10=X10, X20=X20, X30=X30, X40=X40, X50=X50, X60=X60)
```

Rvest에서 크롤링한 데이터와 RSelenium 데이터를 cbind로 묶는다.

```R
tdf <- cbind(tdf, infoDF)
```

하나로 묶여진 데이터는 월별로 sheet를 나누어 xlsx 파일로 내보낸다.

```R
sheName <- paste(month_eng[mcnt], sep='')
file <- paste('resources/', y, '_view_data_soc.xlsx', sep='')
write.xlsx(df, file, sheetName=sheName, col.names=T, row.names=F, append=T, password=NULL, showNA=T)
```

***

# MySQL Database 연동

매번 수십개의 엑셀 파일을 열고 데이터를 불러오는 시간이 길어 MySQL 데이터베이스를 이용해서 데이터를 관리하려 한다. 이를 위해 MySQL을 설치 후 R에서 RMySQL 패키지와 DBI 패키지를 이용해 엑셀 데이터를 import 후 사용한다.

## MySQL Connection

R은 RMySQL 패키지를 제공하며 별도의 드라이버 설치 없이 패키지 로드로 간단히 접속할 수 있다.

```R
conn <- dbConnect(MySQL(), user="naver", password="Naver1q2w3e4r!", dbname="naverdb",host="localhost")
```

conn 객체가 무사히 생성되면 접속이 된 것이다.

## Data Send

엑셀 데이터를 추출하여 데이터베이스로 업로드 하기 위해 엑셀의 데이터를 모두 가져오며, 각 시트(월)의 데이터도 가져올 수 있도록 함수를 만들었다. 카테고리 별 엑셀 파일을 가져와서 시트별 data frame으로 만들어 send 함수를 통해 데이터베이스에 적재한다.

다음은 data frame의 데이터를 각 변수에 담고 속성을 붙여 정리하는 과정이다. 이 때, 날짜의 가독성을 위해 `/`로 구분을 지어주었다.

```R
NEWSDATE <- c()
for(l in c(1:len)) {
    DATE <- paste(str_sub(date[l], 1, 4), '/', str_sub(date[l], 5, 6), '/', str_sub(date[l], 7, 8), sep='')
    NEWSDATE <- c(NEWSDATE, DATE)
}
NVIEW <- df$view
NCOMMENT <- df$cmt
CURR_CMT <- df$currCmt
DELETED <- df$deleted
BROKEN <- df$brokenPolicy
MALER <- df$maleRatio
FEMALER <- df$femaleRatio
X10 <- df$X10; X20 <- df$X20; X30 <- df$X30; X40 <- df$X40; X50 <- df$X50; X60 <- df$X60
```

해당 테이블의 primary key역할을 할 NEWSID값을 만들기 위해 각 뉴스의 세션의 첫 글자와 댓글 랭킹인지, 조회수 랭킹인지 알아볼 수 있도록 하기 위해 `C`, `V` 태그를 붙인 후 특정 숫자를 붙여 중복된 데이터가 들어가지 않도록 하였다.

```R
if(is.null(NVIEW)) {
    NVIEW <- rep(0, len)
    for(l in c(1:len)) {
        ID <- paste(type, 'C', date[l], NEWSRANK[l], sep='')
        # cat(ID, '\n')
        NEWSID <- c(NEWSID, ID)
    }
}
else {
    NCOMMENT <- rep(0, len)
    for(l in c(1:len)) {
        ID <- paste(type, 'V', date[l], NEWSRANK[l], sep='')
        # cat(ID, '\n')
        NEWSID <- c(NEWSID, ID)
    }
}
```

