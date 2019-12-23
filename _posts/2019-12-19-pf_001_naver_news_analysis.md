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

# Full Source

[GitHub Repository](https://github.com/littlecsi/tjproject)

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
| 지운 댓글수 | 댓글 작성자가 지운 댓글수 |
| 지워진 댓글수 | 규정 위반으로 인해 지워진 댓글수 |
| 성별 비율 | |
| 연령대 비율 | 10대, 20대, 30대, 40대, 50대, 60대 이상으로 분류 |

***

# Crawling

## R Selenium

RSelenium 패키지를 사용하여 크롤링 하였다. Selenium을 사용하는 이유는 rvest패키지로 GET이나 POST로 가져올 수 없는 경우 Selenium은 가능하기 때문이다. 예를 들어 클릭해서 로그인 후 내용을 크롤링 하거나, 검색어를 입력해서 크롤링 하는 경우, 웹표준을 지키지 않아서 크롤링이 어려운 경우 등에 사용한다.

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

이제 각 변수에 들어가 있는 데이터들을 query에 넣어 DBI 패키지의 `dbSendQuery()`함수를 사용하여 send한다.

```R
for(l in c(1:len)) {
    query <- paste("INSERT INTO ", tab, " VALUES(\'", NEWSID[l], '\', ', NEWSRANK[l], ', \'', TITLE[l], '\',\'', SUBTITLE[l], '\',\'',  SRC[l], '\',\'', NEWSDATE[l], '\', ', NVIEW[l], ', ', NCOMMENT[l], ', ', CURR_CMT[l], ', ', DELETED[l], ', ', BROKEN[l], ', ', MALER[l], ', ', FEMALER[l], ', ', X10[l], ', ', X20[l], ', ', X30[l], ', ', X40[l], ', ', X50[l], ', ', X60[l], ")", sep='')
    
    dbSendQuery(conn, query)
}
```

위의 send 함수를 각 뉴스 세션(6개)과 시트(12개월)로 반복하여 실행한다. 다음 코드로 섹션과 시트 개수만큼 반복하게된다.

```R
for(i in c(1:6)) {
  type <- types[i]
  section <- sections[i]
  tab <- tables[i]
  fpath <- paste(path, section, '/2018_view_data_', section, ext, sep='')
  for(i in c(1:12)) {
    df <- read.xlsx(fpath, sheet=i, colNames=T, rowNames=F)
    df <- cleanData(df)
    dbsend(df, type, section, tab)
    cat('-', i, '-\n')
  }
}
```

## Database Select

데이터베이스에 올라간 데이터를 받아오는 함수를 모아두는 스크립트를 별도로 관리하였다. 모든 데이터 분석 스크립트에 최상단에 `source()`함수로 받아와 함수를 R의 Environment에 올려둔다.

다음은 DB에서 데이터를 가지고 오는 함수이다.

```R
getSectionData <- function(section, type) {
    query01 <- paste('select * from news_',section,' where newsid like "%',type,'%"', 'and newsid not like "%1911%"', sep = '')
    dfOne <- dbGetQuery(conn, query01)
    return(dfOne)
}
```

매개변수로 뉴스 카테고리와 타입(댓글수, 조회수)을 받아와 2019년 11월 데이터를 제외한 데이터를 데이터베이스로부터 모든 컬럼을 받아오게된다.

***

# 데이터 분석, 시각화 및 예측

Crawling된 데이터를 분석하고 다양한 가설을 여러 검정 방법을 통해 유의 수준(p-value)을 측정해 채택하는 과정을 거친다. 또 그래프 등으로 데이터를 시각화하여 데이터의 분포와 방향성을 한 눈에 알아볼 수 있도록 한다. 그리고 댓글 작성자의 데이터를 통해 뉴스 섹션을 예측해보는 알고리즘을 적용하여 뉴스 카테고리 선택이 예측 가능하다는 것을 증명한다.

***

# 카이 제곱 검정

네이버 뉴스의 각 세션의 카테고리의 댓글수가 일 년 평균의 댓글수, 조회수와 차이가 있는지 검정을 했다.

## 귀무 가설

> 각 카테고리의 댓글수, 조회수는 일 년 평균의 댓글수와 차이가 없다.

## 데이터 셋

각 카테고리의 월별 comment 합계를 사용했으며, 각 행은 1월부터 12월까지이다.

- 댓글수

| Economy | IT     | Life_Cult | Politics | Society | World  |
|:---------:|:--------:|:-----------:|:----------:|:---------:|:--------:|
| 659886  | 112092 | 260844    | 1911107  | 1506685 | 305898 |
| 515121  | 148336 | 194695    | 1717366  | 1365131 | 255383 |
| 573704  | 118948 | 288874    | 2218102  | 1709256 | 354731 |
| 407151  | 108718 | 154541    | 1939494  | 1088885 | 206754 |
| 496035  | 96606  | 178784    | 2021505  | 1113531 | 271456 |
| 414582  | 94331  | 209414    | 1496470  | 1026012 | 262090 |
| 774258  | 110048 | 286352    | 1857602  | 1143347 | 541489 |
| 613186  | 146976 | 331226    | 2443301  | 1590230 | 582676 |
| 394022  | 129072 | 254382    | 2972514  | 1950946 | 337660 |
| 436861  | 126394 | 277971    | 2222571  | 1861683 | 312727 |
| 678359  | 146599 | 254832    | 3278917  | 2554161 | 270679 |
| 700864  | 127796 | 206871    | 1959943  | 1463843 | 300111 |

- 조회수

| Economy | IT | Life_Cult | Politics | Society | World |
|:---------:|:--------:|:-----------:|:----------:|:---------:|:--------:|
| 100360767 | 24318368 | 66185551 | 94377073 | 224854699 | 56742533 |
| 82605339 | 26571839 | 60148342 | 81224742 | 195040992 | 52918607 |
| 92725836 | 22961125 | 81840238 | 102367831 | 274495875 | 69620547 |
| 64376011 | 26225649 | 29612244 | 90189555 | 171676720 | 45618439 |
| 58151423 | 17556183 | 30631936 | 79373214 | 143327966 | 49052918 |
| 47012168 | 15552585 | 36170789 | 69076368 | 148542046 | 54264870 |
| 74764867 | 20546391 | 47437879 | 80397013 | 155554583 | 66601541 |
| 81484980 | 31272142 | 64107155 | 99438941 | 149975828 | 86008202 |
| 59604523 | 28324852 | 58441438 | 113892992 | 152266161 | 63514743 |
| 56800322 | 27399146 | 53852995 | 90718911 | 163767600 | 67452310 |
| 184035809 | 36879388 | 80367084 | 178246245 | 383292716 | 66053212 |
| 106837908 | 26320105 | 88204415 | 45959082 | 242464417 | 74342050 |

## 데이터 전처리

위의 데이터를 ggplot에 사용하기 위해 Month, variable, value 컬럼으로 구분하여 melt하였다.

```R
CmtPlotData1 <- melt(CmtTotaldf, id.vars="Month")
```

melt된 결과는 다음과 같다.

| Month | variable | value  |
|:-------:|:----------:|:--------:|
| Jan   | Economy  | 659886 |
| Feb   | Economy  | 515121 |
| Mar   | Economy  | 573704 |
| Apr   | Economy  | 407151 |
| May   | Economy  | 496035 |
| Jun   | Economy  | 414582 |
|...|...|...|

그래프를 그릴때, 월별 순서를 일정하게 하기 위해 Month컬럼은 factor로 순서를 맞춰 다시 만들어 넣었다.

```R
# Order the x variable into the order we want.
xorder <- c("Nov","Dec","Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct")
CmtPlotData1$Month <- factor(CmtPlotData1$Month, levels=xorder, labels=xorder)
```

## 데이터 시각화

월별 총 댓글수와 조회수를 카테고리별로 구분하여 시각화 하였다.

- 댓글수
![commentplot](/assets/img/pf/nv-chisq-com01.png)
- 조회수
![viewplot](/assets/img/pf/nv-chisq-view01.png)

```R
options("scipen" = 100)
CmtPlot1 <- ggplot(CmtPlotData1, aes(Month, value, col=variable)) +
    geom_point() + 
    geom_line(aes(color=variable, group=variable)) + 
    labs(title="Total Number of Comments per Month (2018.11 ~ 2019.10)")
CmtPlot1
```

위 그래프를 보면 일 년간의 댓글수(위)와 조회수(아래)의 추이를 볼 수 있다. 

- 댓글수 랭킹 그래프(위)
  - 정치와 사회 섹션은 대체로 비슷한 모양을 보이며 변동폭이 크다는 것을 볼 수 있으며, 경제와 세계 섹션은 정치, 사회에 비해 적은 변동폭을 볼 수 있지만 IT와 문화섹션은 변동이 비교적 적은 것을 볼 수 있다.
- 조회수 랭킹 그래프(아래)
  - IT를 제외하면 대체로 변동폭이 큰것으로 보인다. 댓글수 그래프와 비교하면 댓글은 정치 댓글이 많았지만 조회수는 사회 섹션이 가장 많은 것으로 보인다.

## 카이 제곱 검정 수행

그래프로 확인 할 수 있는 부분을 카이 제곱 분석을 통해 확인해본다. 6가지 섹션을 한번에 검정하기 위해 반복문을 사용하였다.

```R
for(i in c(1:6)) {
    testing <- cbind(chi_df[,i]/1000000, rep(sum(chi_df[,i]) / 12, 12)/1000000)
    res_df[i] <- round(chisq.test(testing, correct = F)$p.value, 4)
}
```

댓글수와 조회수의 수의 크기가 크기 때문에 댓글수는 1만, 조회수는 100만으로 나누어 정규화하였다. 카이 제곱 검정을 수행한 결과의 p-value는 다음 표로 정리하였다.

- 댓글수 검정 결과

| Economy | IT | Life_Cult |
|--------|----------|------|
| 0.1171 | 0.9994 | 0.8414 |
| Politics | Society | World |
| 0.0000 | 0.0000 | 0.0747 |

- 조회수 검정 결과

| Economy | IT | Life_Cult |
|--------|----------|------|
| 0.0000 | 0.7604 | 0.0000 |
| Politics | Society | World |
| 0.0000 | 0.0000 | 0.4039 |

## 결과 분석

카이 제곱 검정 결과를 보아, 월간 총 댓글수는 IT(0.99)와 문화(0.84), 경제(0.11), 세계(0.07) 섹션에서 p-value가 0.05 이상으로 **일 년 평균의 댓글 수와 각 월의 댓글 수가 서로 차이가 없는 것**으로 나타났으며,
정치(0), 사회(0)는 p-value가 0.05 이하로 귀무 가설이 기각되었다.
이는 **정치와 사회 카테고리의 댓글 수는 월별 평균과 차이가 있음**으로 결론지을 수 있다.

월간 총 조회수 검정 결과, IT(0.76)와 세계(0.40)는 p-value가 0.05 이상으로 귀무 가설이 채택되었다. 따라서 IT와 세계 섹션은 **일 년 평균의 조회수와 각 월의 조회수가 차이가 없는 것**으로 나타났다. 그리고 경제(0), 문화(0), 정치(0), 사회(0) 섹션은 p-value가 0.05 이하로 귀무 가설이 기각 됨으로 인해 **일 년 평균 조회수와 각 월의 조회수가 차이가 있는 것**으로 나타났다.

## 결론

네이버 뉴스 기사에서 IT, 세계 섹션은 일 년 내내 평이한 조회수와 댓글수가 달리며, 정치와 사회 섹션은 일 년간 조회수와 댓글수의 변동이 크다는 것을 알 수 있다. 급격하게 조회수 및 댓글수가 올라가는 시점의 기사 제목과 내용을 분석해보면 의미 있는 결과가 나올 것으로 추정한다.

***

# 두 집단 모분산비 검정(F-test to compare two variances)

두 집단 모분산비 검정, 즉 두 집단이 서로 동질한지 검정하는 F-test(var.test)를 수행한다.

## 귀무 가설

> 두 뉴스 분야간의 분포의 모양이 동질적이다.

## 데이터 셋 및 전처리 과정과 시각화

카이 제곱 검정에서 사용한 데이터셋을 같이 공유하며 그래프도 동일하게 나온다. 

- 댓글수
![commentplot](/assets/img/pf/nv-chisq-com01.png)
- 조회수
![viewplot](/assets/img/pf/nv-chisq-view01.png)

위 그래프를 보면 일 년간의 댓글수(위)와 조회수(아래)의 추이를 볼 수 있다. 

- 댓글수 랭킹 그래프(위)
  - 정치와 사회 섹션은 대체로 비슷한 모양을 보이며, 경제와 세계 섹션은 정치, 사회에 비해 적은 변동폭을 볼 수 있지만 IT와 문화섹션은 변동이 비교적 적은 것을 볼 수 있다.
- 조회수 랭킹 그래프(아래)
  - IT를 제외하면 대체로 변동폭이 큰것으로 보인다. 댓글수 그래프와 비교하면 댓글은 정치 댓글이 많았지만 조회수는 사회 섹션이 가장 많은 것으로 보인다.

## 동질성 검정 수행

각 세션을 cross하여 2개 세션이 동질한지 검정해본다. 동질성 검정을 위해 `var.test()`함수를 사용했으며, 6가지 세션을 서로 비교하도록 반복문을 실행하였다.

```R
for(i in c(1:6)) {
    for(j in c(1:6)) {
        test <- var.test(x=CmtTotaldf[,i], y=CmtTotaldf[,j])
        resultDf[i,j] <- round(test$p.value, 4)
    }
}
```

`var.test`의 결과값은 cross table 형태로 나타냈으며 소숫점 4자리 까지 표현하였다. 

- 댓글수 검정 결과

|  | Economy | IT | Life_Cult | Politics | Society | World |
|:-------:|:-----:|:-----:|:-------:|:-------:|:-----:|:-----:|
| Economy | 1 | 0 | 0.0055 | 0.0012 | 0.0074 | 0.6721 |
| IT | 0 | 1 | 0.0019 | 0 | 0 | 0 |
| Life_Cult | 0.0055 | 0.0019 | 1 | 0 | 0 | 0.0161 |
| Politics | 0.0012 | 0 | 0 | 1 | 0.4906 | 0.0003 |
| Society | 0.0074 | 0 | 0 | 0.4906 | 1 | 0.0024 |
| World | 0.6721 | 0 | 0.0161 | 0.0003 | 0.0024 | 1 |

- 조회수 검정 결과

|  | Economy | IT | Life_Cult | Politics | Society | World |
|:-------:|:-----:|:-----:|:-------:|:-------:|:-----:|:-----:|
| Economy | 1 | 0.0002 | 0.8079 | 0.6159 | 0.0091 | 0.0545 |
| IT | 0.0002 | 1 | 0.0003 | 0.0007 | 0 | 0.0322 |
| Life_Cult | 0.8079 | 0.0003 | 1 | 0.7955 | 0.0048 | 0.0897 |
| Politics | 0.6159 | 0.0007 | 0.7955 | 1 | 0.0024 | 0.1466 |
| Society | 0.0091 | 0 | 0.0048 | 0.0024 | 1 | 0 |
| World | 0.0545 | 0.0322 | 0.0897 | 0.1466 | 0 | 1 |

## 결과 분석

댓글수 랭킹 기사의 F-Test 결과를 보면, p-value가 0.05 이상인 뉴스 섹션들은 경제/세계(0.6721), 사회/정치(0.4906) 섹션이 각각 채택되었다. '두 뉴스 분야간의 분포의 모양이 동질적이다.'라는 귀무 가설을 채택함으로써, **경제와 세계 섹션, 사회와 정치 섹션의 분포의 모양이 서로 비슷하다**고 결론지을 수 있다.

조회수 랭킹 기사의 F-Test 결과를 보면, p-value가 0.05 이상인 뉴스 섹션들은 경제/문화(0.80), 경제/정치(0.61), 경제/세계(0.05), 문화/정치(0.79), 문화/세계(0.08), 정치/세계(0.14) 섹션이 각각 채택되었다. 이로 인해 **경제와 문화, 경제와 정치, 경제와 세계, 문화와 정치, 문화와 세계, 정치와 세계, 총 6개 섹션의 분포와 모양이 서로 비슷하다**고 결론지을 수 있다. 

## 결론

댓글수 랭킹 기사는 사회와 정치, 경제와 세계의 분포 모양이 동질하다는 분석 결과로 인해 이 두 그룹은 어떤 사건이 발생할 때 비슷한 경향의 관심도(댓글수)를 보여준다고 볼 수 있다. 

조회수 랭킹 기사는 전체 15가지의 경우의 수 중에 6가지의 경우가 채택되었다. 교차표를 살펴보면 IT와 사회 섹션의 경우 다른 섹션과 비슷한 성질의 분포 모양을 가지지 않는다는 것을 알 수 있다. 따라서 IT와 사회를 제외한 섹션들은 대체로 비슷한 경향을 가지지만, IT와 사회 섹션은 다른 분야의 뉴스 조회수와 무관한 경향을 가진다.

***

# 의사 결정 트리

의사 결정 트리를 통해 댓글 작성자의 성별과 연령대 데이터를 통해 카테고리를 예측하는 방법을 알아본다.

## 연구 과정

1. 종속 변수는 카테고리, 독립 변수는 성별과 연령을 두어 성별과 연령대를 전체 댓글 수에 비례하도록 나눈다. 
2. 특정 연령, 성별을 넣었을 때, 카테고리가 결정되도록 한다.

## 데이터 셋

성별과 연령대의 비율을 전체 댓글수에 대입시켜 raw data를 도출하였다. 

```R
for(i in c(1:nrow(tree_df))) {
    for(j in c(5:12)) {
        tree_df[i,j] <- round(tree_df[i,1] * tree_df[i,j] / 100, 2)
    }
}
```

| MALER | FEMALER | X10 | X20 | X30 | X40 | X50 | X60 | cat |
|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|
| 2021.04 | 384.96 | 0 | 168.42 | 721.8 | 890.22 | 457.14 | 168.42 | E |
| 644.91 | 132.09 | 7.77 | 108.78 | 225.33 | 256.41 | 132.09 | 46.62 | E |
| 463.55 | 171.45 | 6.35 | 25.4 | 165.1 | 247.65 | 133.35 | 57.15 | E |
| 489.01 | 129.99 | 6.19 | 105.23 | 191.89 | 167.13 | 105.23 | 43.33 | E |
| 505.8 | 56.2 | 0 | 16.86 | 118.02 | 213.56 | 157.36 | 56.2 | E |
| 417.24 | 131.76 | 5.49 | 32.94 | 148.23 | 203.13 | 126.27 | 32.94 | E |

Training 데이터와 Test 데이터를 샘플링하기 위해 `createDataPartition` 함수를 사용하였다. 이 때 Training 데이터의 비율은 80%로 하였다.

```R
idx <- createDataPartition(y=treeData$cat, p=0.8, list=F)
train <- treeData[idx,]
test <- treeData[-idx,]
```

## 의사 결정 트리 알고리즘 선택

의사 결정 트리를 생성하는 여러 패키지의 알고리즘이 있다. 뉴스 댓글 작성자 데이터를 넣고 적절한 결과를 도출하는 알고리즘을 선택한다.

1. Tree Package
  ```R
  treeMod <- tree(cat ~ ., data=train)
  ```
  Tree Package의 의사 결정 트리 알고리즘을 적용하여 실행한 결과 다음과 같은 에러가 발생했다. `tree()` 함수를 이용한 결과 single node tree가 만들어진 결과 에러가 발생하였다.
  ```
  Error in plot.tree(treeMod) : cannot plot singlenode tree
  ```
2. Party Package
   ```R
   ctreeMod <- ctree(cat ~ ., data=train)
   ```
   ctree 알고리즘을 적용하여 실행한 결과 Tree Package 의 경우와 같은 에러가 발생하였다.
3. Rpart Package
   ```R
   rpartMod <- rpart(cat ~ ., data=train, method="class")
   ```
   Rpart 패키지의 함수를 이용하여 실행한 결과 오류가 발생하지 않고 분기 지점을 잘 도출하였다.

   ```
    n= 52506 

    node), split, n, loss, yval, (yprob)
        * denotes terminal node

    1) root 52506 43755 E (0.17 0.17 0.17 0.17 0.17 0.17)  
    2) X40< 184.12 30066 21582 I (0.19 0.28 0.26 0.0079 0.019 0.24)  
        4) X50< 22.545 15771  8623 I (0.036 0.45 0.34 0 0.0013 0.17)  
        8) FEMALER< 25.195 13420  6573 I (0.021 0.51 0.3 0 0.00015 0.17) *
        9) FEMALER>=25.195 2351  1005 L (0.12 0.13 0.57 0 0.0077 0.18) *
        5) X50>=22.545 14295  9172 E (0.36 0.093 0.18 0.017 0.039 0.31)  
        10) FEMALER< 108.6 12379  7669 E (0.38 0.1 0.15 0.013 0.012 0.34)  
            20) X10< 0.315 4928  2395 E (0.51 0.07 0.14 0.012 0.012 0.25) *
            21) X10>=0.315 7451  4481 W (0.29 0.13 0.16 0.014 0.012 0.4) *
        11) FEMALER>=108.6 1916  1224 L (0.22 0.027 0.36 0.039 0.22 0.14) *
    3) X40>=184.12 22440 13928 P (0.14 0.012 0.039 0.38 0.36 0.069)  
        6) X60< 117.45 10789  5734 S (0.22 0.022 0.067 0.11 0.47 0.12) *
        7) X60>=117.45 11651  4297 P (0.062 0.0023 0.013 0.63 0.27 0.025) *
   ```
   
   의사 결정 트리를 보면 train 데이터가 가진 속성으로 적절하게 나누어 최종 노드(*)까지 잘 분기되는 모습을 볼 수 있다.

## 의사 결정 트리 시각화

![decisionTree](/assets/img/pf/nv-dectree-01.png)

시각화된 의사 결정 트리를 통해 최종 노드까지 분기되는 과정을 짚어보면, 최상단 분기 지점(1)에서 40대 댓글수를 통해 분기하며, 다음 레벨의 분기 지점에서는 각각 50대(2), 60대(3) 댓글수를 통해 나뉜다. 다음 레벨의 분기 지점(4, 5)에서 여성의 댓글수를 통해 나뉘고, 마지막 분기 지점에서 10대(10)의 댓글 수를 통해 분기 된다.

모든 train 데이터의 각 노드별 분기 기대값은 각각 1/6 확률(16%) 이였지만 IT 노드로 간 데이터는 전체의 25%, 생활은 4%(9)와 4%(11), 경제는 10%, 세계는 15%, 사회 21% 그리고 정치는 22% 비율로 도달했다.

## 의사 결정 트리 예측

predict 함수를 통해 의사 결정 트리의 모델을 이용하여 테스트 데이터를 넣고 정확도를 계산하였다. 정확도를 계산하기 위해 caret Package의 `confusionMatrix`함수를 이용하였다.

```R
confusionMatrix(factor(test$cat), predict(rpartMod, test, type = "class"))
```

Confusion Matrix의 결과이다.

```
Confusion Matrix and Statistics

          Reference
Prediction    E    I    L    P    S    W
         E 2460  456    0 1570  661  322
         I  529 4091    0  153  200  496
         L  664 2241    0  503 1726  335
         P  523   55    0 4483  262  146
         S  861   19    0 1743 2561  285
         W 1373 1495    0 1169  734  698

Overall Statistics
                                         
               Accuracy : 0.4356 
```

위 결과를 보면 예측 데이터에 생활 데이터가 없다고 나왔다. 또한 전체 정확도는 43.56%가 나왔다.

## 결론

네이버 뉴스 댓글의 속성으로 의사 결정 트리 알고리즘을 선정하기 위해 3가지 패키지를 이용하였다. 그 중 tree 패키지와 party 패키지의 ctree 함수는 에러가 발생하여 rpart 패키지의 rpart 함수를 이용하여 의사 결정 트리를 생성했다.

생성된 의사 결정 트리를 이용하여 테스트 데이터를 예측해본 결과 정확도가 43.56%가 나왔다. 따라서 의사 결정 트리는 네이버 뉴스 댓글 속성으로 뉴스의 섹션을 예측하기에 적합하지 않은 알고리즘으로 판단했다.

***

# 인공 신경망

인공 신경망 알고리즘이 구현된 R의 nnet Package를 이용하여 네이버 댓글의 속성으로 뉴스 카테고리 예측을 해본다.

## 데이터 셋

예측할 데이터의 종속변수를 늘리기 위해 댓글의 수와 성별, 연령대 컬럼을 사용하였다. 독립 변수는 뉴스 섹션을 One-hot encoding 방식으로 변환하였다.

## One-hot encoding

`nnet` 함수를 사용하기 위해 section을 각 컬럼으로 나누었다. 이 때 사용한 함수는 `class.ind`을 사용하였다. 

```R
section.ind <- class.ind(NewsData$section)
NewsDataE <- cbind(NewsData, section.ind)
```

### 결과

| ... | section | Economy | IT | Life_Cult | Politics | Society | World |
|:----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
| ... | Life_Cult | 0 | 0 | 1 | 0 | 0 | 0 |
| ... | IT | 0 | 1 | 0 | 0 | 0 | 0 |
| ... | Economy | 1 | 0 | 0 | 0 | 0 | 0 |
| ... | World | 0 | 0 | 0 | 0 | 0 | 1 |
| ... | Life_Cult | 0 | 0 | 1 | 0 | 0 | 0 |
| ... | World | 0 | 0 | 0 | 0 | 0 | 1 |

## 테스트 데이터 선별

One-hot encoding된 데이터들의 80%를 train 데이터로 하고 20%를 test 데이터로 잘라내었다. 이 때 원본 데이터의 각 행을 구별하기 위해 다음과 같이 전처리를 하였다.

```R
Elen <- nrow(subset(NewsDataE, section == "Economy"))
Ilen <- nrow(subset(NewsDataE, section == "IT"))
Llen <- nrow(subset(NewsDataE, section == "Life_Cult"))
Plen <- nrow(subset(NewsDataE, section == "Politics"))
Slen <- nrow(subset(NewsDataE, section == "Society"))
Wlen <- nrow(subset(NewsDataE, section == "World"))

Ei <- sample(c(1:Elen), 0.8*Elen)
Ii <- sample(c(1:Ilen), 0.8*Ilen)
Li <- sample(c(1:Llen), 0.8*Llen)
Pi <- sample(c(1:Plen), 0.8*Plen)
Si <- sample(c(1:Slen), 0.8*Slen)
Wi <- sample(c(1:Wlen), 0.8*Wlen);

idx <- c(
    Ei,
    Ii + Elen,
    Li + Elen + Ilen,
    Pi + Elen + Ilen + Llen,
    Si + Elen + Ilen + Llen + Plen,
    Wi + Elen + Ilen + Llen + Plen + Slen
)

trData <- NewsDataE[idx,]
teData <- NewsDataE[-idx,]
```

각 행의 길이를 구하고 시작점을 찾기 위해 다음 column으로 갈 때 이전 column의 길이를 더한 index 값을 설정하여 train과 test 데이터를 만들었다.

## nnet 적용

`nnet`함수의 매개변수는 x(독립 변수),  y(종속 변수), softmax가 있다. nnet model을 만들기 위해 x에 1열부터 11열까지(댓글 속성), y에 13열부터 18열까지(one-hot encoding된 카테고리)를 넣었으며, softmax에 True 값을 넣어 각 값들 사이가 보간(interpolation)되도록 하였다.

```R
trX <- trData[,c(1:11)]
trY <- trData[,c(13:18)]
teX <- teData[,c(1:11)]

nnModel <- nnet(x=trX, y=trY, size=15, softmax=T)
```

만들어진 nnet model을 시각화 해보았다. 시각화를 하기 위해 nnet plot 함수를 별도로 다운로드 받았다.

```R
# 시각화 R 코드 함수 다운로드
source_url('https://gist.githubusercontent.com/fawda123/7471137/raw/466c1474d0a505ff044412703516c34f1a4684a5/nnet_plot_update.r')

plot.nnet(nnModel)
```

![nnet](/assets/img/pf/nv-nnet-01.png)


시각화된 nnet 모델을 보면 hidden 계층이 하나 있고, 종속변수 11개, 독립 변수 6개와 bias 값이 있는 것을 확인할 수 있다.

## 예측 모델 적용 및 결과 분석

nnet의 결과인 모델을 이용하여 test 데이터로 예측을 수행한다. 예측을 위해 `predict` 함수를 이용했다. 예측 결과물은 table로 만들어 적중률을 도출했다.

```R
nnPrediction <- predict(nnModel, teX, type="class")
predTable <- table(nnPrediction, teData$section)
```

| - | Economy | IT | Life_Cult | Politics | Society | World |
|:---------:|:-------:|:----:|:---------:|:--------:|:-------:|:-----:|
| Economy | 765 | 216 | 183 | 11 | 24 | 407 |
| IT | 93 | 1423 | 557 | 0 | 6 | 342 |
| Life_Cult | 243 | 353 | 956 | 9 | 54 | 663 |
| Politics | 160 | 10 | 59 | 1694 | 586 | 87 |
| Society | 795 | 154 | 338 | 441 | 1493 | 449 |
| World | 132 | 32 | 95 | 33 | 25 | 240 |

교차표로 결과값을 비교한 결과를 바탕으로 정확도를 계산했다.

```R
calcAcc <- function(compTable) {
    len <- nrow(compTable)
    total <- 0
    for(l in c(1:len)) { total <- total + compTable[l,l] }
    accuracy <- round((total / sum(compTable)) * 100, 2)
    return(accuracy)
}
predAccuracy <- cat(calcAcc(predTable), "%\n")
# 50.05 %
```

nnet 모델의 예측 정확도는 50.05%가 나왔다. 평균 예측 정확도를 얻기 위해 5번 반복하여 시각화하고 평균치를 구했다.

```R
df=data.frame(x=c(1:5), y=c(50.05, 50.53, 44.24, 56.92, 50.68))
ggplot(data=df, mapping=aes(x=x, y=y, col=x, fill=x)) +
  geom_col(position="identity", show.legend=F) +
  geom_text(label=paste(df$y, "%"), nudge_y=2, color="black") +
  geom_hline(aes(yintercept=mean(df$y))) +
  geom_text(x=3, y=51, label=paste("mean :", mean(df$y)), check_overlap=T, color="Red") +
  labs(title="nnet result", x="Tries", y="Percentage")
```

![nnet02](/assets/img/pf/nv-nnet-02.png)

위 데이터는 데이터를 5번 샘플링하여 각각 nnet을 적용시킨 결과이다. 평균값은 50.484%가 나왔으며 대체로 정확도가 낮다는 것을 확인할 수 있다.

## 결론

