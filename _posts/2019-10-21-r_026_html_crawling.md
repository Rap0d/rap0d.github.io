---
layout: post
title: 26. HTML Crawling
subtitle: HTML Crawling
categories: study
tags: rprogramming
---

![r](/assets/img/logo/r-logo.png)

## Overview

R에서 `rvest` 패키지를 이용한 HTML 크롤링을 하는 방법을 알아본다.

***

## 예제 1

```R
# install.packages("rvest")

# rvest : R로 크롤링을 할 때 가장 많이 쓰이는 패키지
library(rvest)
library(dplyr)
library(stringr)

url <- 'http://movie.daum.net/moviedb/grade?movieId=112942&type=netizen&page=1'

# read_html 함수를 통해 html을 불러온다.
## encoding UTF-8은 대소문자 구분하니 주의한다.
html01 <- read_html(url, encoding = 'UTF-8')
html01

# id : '#', class : '.'
## html_nodes : node는 tag를 의미, nodes이기 때문에 모두 가져오는 함수
## review_info 클래스를 모두 가져오기
div_lists <- html_nodes(html01, '.review_info')

# 원소의 개수 구하기
length(div_lists)

# lists의 안으로 접근하기
writer_lists <- html_nodes(div_lists, '.link_profile')
writer_lists

# html_text : list의 요소를 가져오는 함수
writer <- html_text(writer_lists)
writer

div_lists

# 텍스트 정제 과정
comments <- html01 %>% html_nodes('.review_info') %>% html_nodes('p') %>% html_text()
comments <- gsub('\\t', '', comments)
comments <- gsub('\\r', '', comments)
comments <- gsub('\\n', '', comments)
comments <- str_trim(comments)
comments
```

***

## 예제 2

```R
library(rvest)

# none page parameter
url_base <- 'https://movie.daum.net/moviedb/grade?movieId=112942&type=netizen&page='

# 10page
last_page <- 10

df <- data.frame()

# all page load
## data frame에 넣기
for (page in 1:last_page) {
  url <- paste(url_base, page, sep = '')
  # cat(url, '\n')
  html <- read_html(url)
  html <- html %>% html_nodes('.review_info')
  writers <- html %>% html_nodes('.link_profile') %>% html_text()
  
  comments <- html %>% html_nodes('p') %>% html_text()
  comments <- gsub('\\t', '', comments)
  comments <- gsub('\\n', '', comments)
  comments <- gsub('\\r', '', comments)
  comments <- gsub(',', '', comments)
  comments <- str_trim(comments)
  comments   
  
  regdate <- html %>% html_node('.info_append') %>% html_text()
  regdate <- gsub('\\t', '', regdate)
  regdate <- gsub('\\n', '', regdate)
  regdate <- gsub('\\r', '', regdate)
  regdate <- gsub(',', '', regdate)
  regdate <- str_trim(regdate)
  
  tmp <- data.frame(writers, comments, regdate)
  df <- rbind(df, tmp)
}
df
colnames(df)
write.csv(df, 'daum2.csv', row.names = F, quote = F)

```

***

## 예제 3

```R
# 네이버 웹툰 페이지 파싱

library(rvest)
library(stringr)

url <- 'https://comic.naver.com/webtoon/weekday.nhn'
u_en <- 'UTF-8'

data <- read_html(url, encoding = u_en)
data

tag01 <- html_nodes(data, '.thumb a')
tag01

# /webtoon/list.nhn?titleId=700858&weekday=mon
attr <- html_attr(tag01, 'href')
attr

attr <- gsub('/webtoon/list.nhn\\?', '', attr)
attr
delimiter <- '&'
a <- str_split(attr[1], delimiter, simplify = T)
delimiter <- '='
b <- str_split(a[1], delimiter, simplify = T)
b

title <- data %>% html_nodes('.thumb') %>% html_node('img') %>% html_attr('title')
title

src <- data %>% html_nodes('.thumb') %>% html_node('img') %>% html_attr('src')
src

# 반복할 전체 갯수
iteration <- c(1:length(attr))
iteration

df <- data.frame()
for (idx in iteration) {
  delimiter <- '&'
  mdata <- str_split(attr[idx], delimiter, simplify = T)
  delimiter <- '='
  titleId <- str_split(mdata[1], delimiter, simplify = T)
  weekday <- str_split(mdata[2], delimiter, simplify = T)
  
  tmp <- data.frame(titleId[2], weekday[2], title[idx], src[idx])
  df <- rbind(df, tmp)
} 
colnames(df) <- c('titleId', 'weekday', 'title','src')
write.csv(df, 'naver_webtoons.csv', row.names = F, quote = T)

```

***

## 예제 4

```R
library(rvest)
library(stringr)

uen <- 'UTF-8'

cleanTxt <- function(x) {
  res <- gsub('\\r', '', x)
  res <- gsub('\\n', '', res)
  res <- gsub('\\t', '', res)
  res <- str_trim(res)
  return (res)
}

# 추가 문제
# https://www.bobaedream.co.kr/cyber/CyberCar.php?gubun=K&page=1
# 페이지에서 필요한 항목만 크롤링해보세요.
#

url1 <- 'https://www.bobaedream.co.kr/cyber/CyberCar.php?gubun=K&page=1'
data01 <- read_html(url1, uen)
data01

item01 <- html_nodes(data01, '.clearfix')
link01 <- item01 %>% html_nodes('.tit a') %>% html_attr('href')
link01 <- paste('https://www.bobaedream.co.kr', link01, sep = '')
title01 <- item01 %>% html_nodes('.tit a') %>% html_attr('title')
desc01 <- item01 %>% html_nodes('.stxt') %>% html_text()
year01 <- data01 %>% html_nodes('#listCont .year') %>% html_text() %>% cleanTxt()
fuel01 <- data01 %>% html_nodes('#listCont .fuel') %>% html_text() %>% cleanTxt()
km01 <- data01 %>% html_nodes('#listCont .km') %>% html_text() %>% cleanTxt()
price <- data01 %>% html_nodes('#listCont .price') %>% html_text() %>% cleanTxt()

res01 <- data.frame(title01, desc01, year01, fuel01, km01, price, link01)
res01

write.csv(res01, '국산차.csv', row.names = F)

# https://gall.dcinside.com/board/lists?id=travel_europe
# 페이지에서 필요한 항목만 크롤링해보세요.
#


# 다음 3개의 사이트에 대하여 크롤링하여 1개의 데이터 프레임에 저장하여 보세요.
# 단, category 컬럼을 하나 추가하여 항목을 구분하도록 하시오.
# https://www.bobaedream.co.kr/cyber/CyberCar.php?cat=2 # 스포츠카
# https://www.bobaedream.co.kr/cyber/CyberCar.php?cat=3 # 밴,RV,버스
# https://www.bobaedream.co.kr/cyber/CyberCar.php?features=camp # 캠핑카
```
