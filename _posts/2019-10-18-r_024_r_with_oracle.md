---
layout: post
title: 24. R과 ORACLE DB 연동하기
subtitle: R과 ORACLE DB 연동하기
categories: study
tags: rprogramming
---

![r](/assets/img/logo/r-logo.png)

# Overview

R과 오라클 Database를 연동하여 데이터를 불러오는 방법을 알아본다.

*** 

# 오라클 데이터 베이스의 데이터를 R으로 가져오기

오라클 데이터 베이스의 데이터를 R으로 가져오기 위해 `DBI`와 `RJDBC` 패키지를 사용하여 데이터를 가져올 수 있다.

## 예제

```R
# install.packages(c("DBI", "RJDBC"))

library(rJava)
library(DBI)
library(RJDBC)

# R에서 DB 접근하기 위한 절차
# 드라이버 객체 구하기
# 접속 객체 구하기

driver <- 'oracle.jdbc.driver.OracleDriver'
jarpath <- '/Users/path_xxx/Documents/_2_WorkSpace/_0_util/ojdbc6.jar'

drv <- JDBC(driverClass = driver, classPath = jarpath)

class(drv)
# [1] "JDBCDriver"
# attr(,"package")
# [1] "RJDBC"

url <- 'jdbc:oracle:thin:@//127.0.0.1:32769/xe'
id <- 'rdbuser'
password <- '1234'

conn <- dbConnect( drv, url, id, password)
class( conn ) 
# [1] "JDBCConnection"
# attr(,"package")
# [1] "RJDBC"

###############

# test query
query = 'select power(2, 10) from dual'

result <- dbGetQuery(conn, query)
result 
# POWER(2,10)
# 1        1024

class( result ) # data.frame

###################

# dbGetQuery(접속객체, 쿼리)
# return : data frame

query <- 'select * from test_table'
resultSet <- dbGetQuery(conn, query)
mode(resultSet) # "list"
class(resultSet) # "data.frame"

query <- ' select * from test_table'
query <- paste(query, ' order by age desc ')
result <- dbGetQuery(conn, query)
result 

query <- "insert into test_table values( 'shin', '1234', '신사임당', 60 )"
dbSendUpdate(conn, query)

query <- 'select * from test_table'
dbGetQuery(conn, query)

query <- 'select * from test_table where age >= 45'
result <- dbGetQuery(conn, query)
result 

query <- "update test_table set age = 40 where name = '강감찬'"
dbSendUpdate(conn, query)

query <- "update test_table set age = 10 where name like '김%'"
dbSendUpdate(conn, query)

query <- 'select * from test_table'
dbGetQuery(conn, query)

query <- "delete from test_table where name = '홍길동'"
dbSendUpdate(conn, query)

query <- 'select * from test_table'
dbGetQuery(conn, query)


```