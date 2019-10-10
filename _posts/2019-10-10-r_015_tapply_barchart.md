---
layout: post
title: 15. plyr 패키지 실습
subtitle: plyr 패키지 실습
categories: study
tags: rprogramming
---

![r](/assets/img/logo/r-logo.png)

## Overview

R의 plyr 패키지 실습을 해본다.

> 예제 파일 : [progbook.csv]({{"/assets/file/r/progbook.csv" | absolute_url}})

*** 

## plyr 패키지 실습

```R
getwd()
setwd('../../R_data/')
install.packages('dplyr')
par(family='D2Coding')

library(plyr)
library(dplyr)
# plyr 패키지와 'progbook.csv' 파일을 이용하여 다음 물음에 답하세요.
# 프로그램의 이름과 판매 수량과 단가를 저장하고 있는 파일의 내용을 읽어 들인다.

# 실습 : 프로그래밍별 총 판매 수량과 총 판매 금액
#     name sum_qty sum_price
# 1  C언어      29     26000
# 2    JSP      15      9500
# 3 Python      35      3500
# 4   자바      19      9000

prog <- read.csv('progbook.csv')
prog


tq <- tapply(prog$qty, prog$name, sum)
tq

tp <- tapply(prog$price, prog$name, sum)
tp

p1 <- data.frame(qty = tq, price = tp)
p1

# 총 판매 금액으로 막대 그래프 그리기 

barplot(tp, col=rainbow(7), main = '총 판매 금액', xlab = 'name', ylab = 'price')

# 실습 : 프로그래밍별 최대 판매 수량과 최소 판매 금액
# 출력 결과
#     name max_qty min_price
# 1  C언어      13      5000
# 2    JSP       7      2000
# 3 Python      15       900
# 4   자바      10      1000

tn <- tapply(prog$qty, prog$name, max)
tn
tx <- tapply(prog$price, prog$name, min)
tx
p2 <- data.frame(qty = tn, price = tx)
p2

# 최소 판매 금액으로 수평 막대 그래프 그리기 

barplot(tx, col=rainbow(7), main = '총 판매 금액', xlab = 'name', ylab = 'price')

# 실습 : 년도별 이름별 최대 판매 수량과 최저 가격 구하기
#    year   name max_qty min_price
# 1  2000  C언어       6      5000
# 2  2000    JSP       7      3500
# 3  2000 Python       9       900
# 4  2000   자바       2      1000
# 5  2001  C언어      10      8000
# 6  2001    JSP       3      2000
# 7  2001 Python      15      1500
# 8  2001   자바       7      3000
# 9  2002  C언어      13     13000
# 10 2002    JSP       5      4000
# 11 2002 Python      11      1100
# 12 2002   자바      10      5000

tyn <- tapply(prog$qty, prog$year, max)
tyn
tyx <- tapply(prog$price, prog$year, min)
tyx
p3 <- data.frame(qty = tyn, price = tyx)
p3
```

### 실행 결과

```R
> # plyr 패키지와 'progbook.csv' 파일을 이용하여 다음 물음에 답하세요.
> # 프로그램의 이름과 판매 수량과 단가를 저장하고 있는 파일의 내용을 읽어 들인다.
> 
> # 실습 : 프로그래밍별 총 판매 수량과 총 판매 금액
> #     name sum_qty sum_price
> # 1  C언어      29     26000
> # 2    JSP      15      9500
> # 3 Python      35      3500
> # 4   자바      19      9000
> 
> prog <- read.csv('progbook.csv')
> prog
   year   name qty price
1  2000  C언어   6  5000
2  2000   자바   2  1000
3  2000    JSP   7  3500
4  2000 Python   9   900
5  2001  C언어  10  8000
6  2001   자바   7  3000
7  2001    JSP   3  2000
8  2001 Python  15  1500
9  2002  C언어  13 13000
10 2002   자바  10  5000
11 2002    JSP   5  4000
12 2002 Python  11  1100
> 
> 
> tq <- tapply(prog$qty, prog$name, sum)
> tq
  자바  C언어    JSP Python 
    19     29     15     35 
> 
> tp <- tapply(prog$price, prog$name, sum)
> tp
  자바  C언어    JSP Python 
  9000  26000   9500   3500 
> 
> p1 <- data.frame(qty = tq, price = tp)
> p1
       qty price
자바    19  9000
C언어   29 26000
JSP     15  9500
Python  35  3500
> 
> # 총 판매 금액으로 막대 그래프 그리기 
> 
> barplot(tp, col=rainbow(7), main = '총 판매 금액', xlab = 'name', ylab = 'price')
> 
> # 실습 : 프로그래밍별 최대 판매 수량과 최소 판매 금액
> # 출력 결과
> #     name max_qty min_price
> # 1  C언어      13      5000
> # 2    JSP       7      2000
> # 3 Python      15       900
> # 4   자바      10      1000
> 
> tn <- tapply(prog$qty, prog$name, max)
> tn
  자바  C언어    JSP Python 
    10     13      7     15 
> tx <- tapply(prog$price, prog$name, min)
> tx
  자바  C언어    JSP Python 
  1000   5000   2000    900 
> p2 <- data.frame(qty = tn, price = tx)
> p2
       qty price
자바    10  1000
C언어   13  5000
JSP      7  2000
Python  15   900
> 
> # 최소 판매 금액으로 수평 막대 그래프 그리기 
> 
> barplot(tx, col=rainbow(7), main = '총 판매 금액', xlab = 'name', ylab = 'price')
> 
> # 실습 : 년도별 이름별 최대 판매 수량과 최저 가격 구하기
> #    year   name max_qty min_price
> # 1  2000  C언어       6      5000
> # 2  2000    JSP       7      3500
> # 3  2000 Python       9       900
> # 4  2000   자바       2      1000
> # 5  2001  C언어      10      8000
> # 6  2001    JSP       3      2000
> # 7  2001 Python      15      1500
> # 8  2001   자바       7      3000
> # 9  2002  C언어      13     13000
> # 10 2002    JSP       5      4000
> # 11 2002 Python      11      1100
> # 12 2002   자바      10      5000
> 
> tyn <- tapply(prog$qty, prog$year, max)
> tyn
2000 2001 2002 
   9   15   13 
> tyx <- tapply(prog$price, prog$year, min)
> tyx
2000 2001 2002 
 900 1500 1100 
> p3 <- data.frame(qty = tyn, price = tyx)
> p3
     qty price
2000   9   900
2001  15  1500
2002  13  1100
```