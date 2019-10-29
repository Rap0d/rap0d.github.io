---
layout: post
title: 29. 기술 통계(Descriptive Statistics) 개요
subtitle: 기술 통계(Descriptive Statistics) 개요
categories: study
tags: rprogramming
---

![r](/assets/img/logo/r-logo.png)

## Overview

기술 통계(Descriptive Statistics)란 자료를 요약해주는 기본적인 통계량을 의미한다.

### 기술 통계량의 사용 목적

전체적인 데이터의 개략적인 분포와 통계적 수치를 제공한다.

모집단의 특성을 유추하는데 사용될 수 있다.

명목 척도를 대상으로 하는 빈도 분석과 비율 / 등간 척도에 많이 사용되는 기술 통계 분석 등이 있다.

***

## 빈도 분석

빈도 분석(Frequency Analysis)은 설문 조사 결과에 대한 가장 기초적인 정보를 제공해주는 분석 방법이다.

특히 성별이나 직급을 수치화하는 명목 척도나 서열 척도 같은 범주형 데이터를 대상으로 비율을 측정하는데 주로 이용된다.

### 빈도 분석

- 범주형 데이터에 대한 비율을 알아보고자 하는 경우 사용
- `table()` 함수 사용

### 빈도 분석의 응용 사례

- 특정 후보의 지지율은 50%이다.
- 응답자 중에서 남자는 30%, 여자는 70%이다.
- 연령대 별 차지하는 비율 등.

***

## 기술 통계 분석

각 척도에 따른 기술 통계량 분석은 다음 표를 이용하여 분석하면 된다.

| 항목 | 명목 | 서열 | 등간(설문 조사) | 비율 |
|:--------:|:--------:|:--------:|:--------:|:--------:|
| 기술 통계량(summary) | X | X | O | O |
| 빈도 수(table) | O | O | O | O(범주화 될 때) |
| 비율(prop.table) | O | O | O | O(범주화 될 때) |
| 그래프 | 막대 | 막대 | 막대, 파이 | 히스토그램, 산점도 |

비율 척도인 경우 빈도 수를 구하려면 코딩 변경을 해서 범주화 척도로 변경을 해야 한다. 예를 들어서 "나이 → 연령대", "점수 → 학점" 등으로 변경해야 한다.

***

## 기술 통계량 보고서 작성

빈도 분석과 기술 통계 분석을 통해서 구해진 기초 통계량 정보를 제시하기 위해서 다음과 같이 표본의 통계적 특성 결과표를 작성한다.

| 변수1 | 변수2 | 빈도수 | 구성 비율(%) |
|:--------:|:--------:|:--------:|:--------:|
| 성별 | 남 | 1,500 | 5O |
| 성별 | 여 | 1,500 | 5O |
| 나이 | 청년층 | 2,065 | 68.83 |
| 나이 | 중년층 | 935 | 31.17 |
| 광고 유형 | 연예인 CF | 1,489 | 49.63 |
| 광고 유형 | 일반인 CF | 1,511 | 5O.37 |
| 관심도 | 관심 있음 | 2,460 | 82.00 |
| 관심도 | 관심 없음 | 540 | 18.00 |

***

## 주요 패키지

- *doBy* 패키지
- *Hmisc* 패키지
- *prettyR* 패키지

### doBy 패키지

```R
install.packages('doBy')
library(doBy)
# 성별로 그룹핑하여 brandA 에 대한 평균을 구해준다.
myttest01 <- summaryBy(brandA ~ gender, ttest01)
myttest01
# gender brandA.mean
# 1 남자 2.95
# 2 여자 3.20
```

### Hmisc 패키지

전체 데이터 셋에 포함된 모든 변수들을 대상으로 기술 통계량을 제공하며, 빈도와 비율 데이터를 일괄적으로 제공해준다.

`describe()` 함수를 자주 사용한다.

특히 데이터 내 결측치(*NA*)의 존재 및 서로 다른 값(*unique*)의 수를 알려주는 점이 편리하다.

`describe()` 함수는 변수의 척도에 따라서 서로 다른 통계량을 제공해준다.

| 변수1 | 변수2 |
|:--------:|:--------:|
| 명목, 서열, 등간 척도 | 빈도수와 비율 등을 제공 |
| 비율 척도 | mean, lowest, hightest 등의 통계량 제공 |

`summary()` 함수를 이용하여 기술 통계량을 구할 수 있지만, `describe()` 함수를 사용하면 좀더 유용한 통계량 등을 얻을 수 있다.

### prettyR 패키지

**Hmisc** 패키지에서 제공하는 `describe()` 함수와 유사한 `freq()` 함수를 제공한다.

`freq()` 함수는 변수별 빈도수, 결측치 비율을 제공해준다.

비율은 소수점까지 제공하며, 각 명목 척도의 변수를 대상으로 *NA* 의 비율까지 제공한다.

***

## 기술 통계 예제

> 예제 파일 : [191029.zip]({{"/assets/file/r/191029/191029.zip" | absolute_url}}) 

### 실습 코드 1

```R
descriptive <- read.csv('descriptive.csv')

head(descriptive)
dim(descriptive)
str(descriptive)

# 명목 척도
summary(descriptive)
length(descriptive)
table(descriptive)

descriptive <- subset(descriptive, descriptive$gender == 1 | descriptive$gender == 2)
genderTable <- table(descriptive$gender)
descriptive
genderTable

barplot(genderTable)

# prop.table() : 비율 구하는 함수
genderProp <- round(prop.table(genderTable) * 100, 2)
genderProp

resiUni <- unique(descriptive$resident)
barplot(resiUni)

# 서열 척도 : level(학력 수준)
unique(descriptive$level)
length(descriptive$level)
summary(descriptive$level) # 의미 없음
table(descriptive$level)

tableLevel <- table(descriptive$level)
barplot(tableLevel)

# 등간 척도 : survey(설문 조사)
survey <- descriptive$survey
survey


# 비율 척도 : cost(비용)
length(descriptive$cost)
summary(descriptive$cost)
mean(descriptive$cost)

## 산점도
plot(descriptive$cost)
## boxplot
costBox <- boxplot(descriptive$cost)
costBox
### 결과의 stats에서 1, 5번째가 이산치
## 이산치 제거
descriptive <- subset(descriptive, descriptive$cost >= 2 & descriptive$cost <= 10)
descriptive

cost <- descriptive$cost

sort(cost)
sort(cost, decreasing = T)
mean(cost)
median(cost)
### quantile : 사분위수 구하는 함수
quantile(cost, 1/4)
quantile(cost, 2/4)
quantile(cost, 3/4)
quantile(cost, 4/4)
## 이산치가 제거된 box
newBox <- boxplot(cost)
newBox
## age별 cost 산점도 그래프
plot(descriptive$age, descriptive$cost)

hist(cost)

# 연속형인 'cost' 컬럼을 범주화(코딩 변경)
# low(4.0 미만), middle(4.0이상 6.0미만), high(6.0이상)
descriptive$cost2[descriptive$cost < 4.0] = "low"
descriptive$cost2[descriptive$cost >= 4.0 & descriptive$cost < 6.0] = "middle"
descriptive$cost2[descriptive$cost >= 6.0] = "high"
unique(descriptive$cost2)

tableCost <- table(descriptive$cost2)
tableCost
barplot(tableCost)

# age 컬럼을 이용하여 age2 코딩 변경
range(descriptive$age)
descriptive$age2[descriptive$age < 50] = "중년"
descriptive$age2[descriptive$age >= 50 & descriptive$age < 60] = "장년"
descriptive$age2[descriptive$age >= 60] = "노년"
unique(descriptive$age2)

# 왜첨도 패키지
# install.packages('moments')
library(moments)
# 왜도
skewness(cost)
# 첨도
kurtosis(cost)

kurt <- kurtosis(cost)
ifelse(kurt == 3.0, '정규분포', ifelse(kurt > 3.0, '뾰족', '완만'))

hist(cost, freq = F)

lines(density(cost), col = 'blue')

x <- seq(0, 8, 0.1)
curve(dnorm(x, mean(cost), sd(cost)), col = 'red', add=T)

dnorm(x, mean(cost), sd(cost))

# install.packages('Hmisc')
## 전체 데이터 셋에 포함된 모든 변수들을 대상으로 기술 통계량을 제공
## 빈도와 비율 데이터를 일괄적으로 제공
library(Hmisc)

Hmisc::describe(descriptive)
Hmisc::describe(descriptive$cost2)

# install.packages('prettyR')
library(prettyR)
prettyR::freq(descriptive)

```

### 실습 코드 2

```R
library(Hmisc)
library(prettyR)

coupData <- read.csv('mycoupon.csv')

par(family = 'AppleGothic')

# 풀어 봅시다.
# ############################################################
# # 쿠폰 01.기본 기술 통계
# ############################################################
# 앞 6행만 보여 주세요.
head(coupData)
# 뒷 6행만 보여 주세요.
tail(coupData)
# View 함수를 이용하여 데이터를 조회해 보세요.
View(coupData)
# 데이터의 구조(structure)를 확인하세요.
str(coupData)
# 기초 요약 통계량 정보를 확인해보세요.
summary(coupData)
# ############################################################
# # 쿠폰 02.쿠폰 유형 기술 통계
# ############################################################
# coupon 정보를 조회해보세요.
coupData$coupon
# factor() 함수를 이용하여 할인 쿠폰/적립 쿠폰이라는 레이블을 새롭게 만들어 보세요.
coupData$coupType <- factor(coupData$coupon, levels = c('1', '2'), labels = c('할인쿠폰', '적립쿠폰'))
# 쿠폰 유형별 분할표를 만들어 보세요.
coupTypeTable <- table(coupData$coupType)
coupTypeTable
# 분할표에 있는 쿠폰들의 유형에 대한 비율을 구해보세요.(prop.table)
propCoupType <- prop.table(coupTypeTable)
propCoupType
# 쿠폰 유형별 갯수에 대한 막대 그래프를 그려 보세요.
barplot(coupTypeTable, col = rainbow(nrow(coupTypeTable)), main = '쿠폰 유형별 갯수')
# ############################################################
# # 쿠폰 03.쿠폰 사용 분야 기술 통계 
# ############################################################
# # category 컬럼 정보를 조회해보세요.
coupData$category
# factor() 함수를 이용하여 category2 컬럼을 생성하시오.
# # 1 : food, 2 : beauty, 3 : travel, 4 : park라고 가정한다.
coupData$cate2 <- factor(coupData$category, levels = seq(1:4), labels = c('food', 'beauty', 'travel', 'park'))
coupData$cate2
# category 유형별 분할표를 만들어 보세요.
coupCateTable <- table(coupData$cate2)
# addmargins() 함수를 이용하여 소계 컬럼을 추가해 보세요.
coupCateTable <- addmargins(coupCateTable)
coupCateTable
# 분할표에 있는 항목들에 대한 비율을 구해보세요.(prop.table)
propCate <- prop.table(coupCateTable)
propCate
# 쿠폰 사용처에 대한 막대 그래프를 그려 보세요.
barplot(coupCateTable, col = rainbow(nrow(coupCateTable)), ylim = c(0, 30), main = '쿠폰 사용처')
# ############################################################
# 참조 그림 : 쿠폰 사용처.png / 쿠폰 유형별 갯수.png
```

### 실습 코드 3

```R
library(Hmisc)
library(prettyR)
library(reshape2)
library(dplyr)

cfData <- read.csv('mycf.csv')

par(family = 'AppleGothic')

##################################################
# 기본 통계량 조회
##################################################

# 앞 6줄만 조회해보세요.
head(cfData)
# 뒷 6줄만 조회해보세요.
tail(cfData)
# View 함수를 이용하여 전체 목록을 조회해보세요.
View(cfData)
# 구조를 확인하세요.
str(cfData)
# 요약 통계량을 조회해보세요.
summary(cfData)
##################################################
# 광고 집단 유형에 대한 기술 통계 분석
##################################################
# group 컬럼은 1이면 연예인을 내세운 광고이고, 
# 2이면, 일반인을 내세운 광고이다.

# factor 함수를 이용하여 요인형으로 변환하세요.
cfData$group2 <- factor(cfData$group, levels = c(1, 2), labels = c('연예인 CF', '일반인 CF'))

# 숫자 1("연예인 CF", 2("일반인 CF")으로 라벨을 붙이세요.
# 이 자료에 대한 빈도 및 비율을 출력하세요.
tableGroup <- table(cfData$group2)
tableGroup
propGroup <- prop.table(tableGroup)
propGroup
# 해당 요인에 대한 그래프를 그리시오.
barplot(propGroup, col = rainbow(nrow(propGroup)))
##################################################
# 광고의 관심 유무에 따른 기술 통계 분석
##################################################
# interest 컬럼은 광고의 관심도이다.
# 1이면 관심이 있고, 0이면 관심이 없다.는 의미이다.
# factor 함수를 이용하여 요인형으로 변환하세요.
cfData$interest2 <- factor(cfData$interest, levels = c(1, 0), labels = c('관심있다', '관심없다'))
# 이 자료에 대한 빈도 및 비율을 출력하세요.
tableInterest <- table(cfData$interest2)
tableInterest
propInterest <- prop.table(tableInterest)
propInterest
# 해당 요인에 대한 그래프를 그리시오.
barplot(tableInterest, col = rainbow(nrow(tableInterest)))
##################################################
# 변수 리코딩을 이용하여 성별을 한글로 보여 주도록 하시오.
# 나이가 40 이하이면 '청년층', 40 초과이면 '중년층'으로 리코딩하시오.
cfData$gender2[cfData$gender == 'M'] = '남'
cfData$gender2[cfData$gender == 'F'] = '여'
tableGender <- table(cfData$gender2)

cfData$age2[cfData$age <= 40] = '청년층'
cfData$age2[cfData$age > 40] = '중년층'
tableAge <- table(cfData$age2)
##################################################
# 기술 통계량 보고서를 작성하세요.
reportCF <- data.frame(성별 = tableGender, 연령층 = tableAge, 관심도 = tableInterest, 광고유형 = tableGroup)
reportCF

Hmisc::describe(cfData)
##################################################
# 참조 그림 : 집단 유형별 샘플수.png / 광고 관심 유무 사용자수.png
```

### 실습 코드 4

```R
library(Hmisc)
library(prettyR)
library(reshape2)
library(dplyr)
library(moments)

descData <- read.csv('descriptive.csv')

par(family = 'AppleGothic')

# descriptive.csv 데이터 셋을 대상으로 다음 조건에 맞게 빈도 분석 및 기술 통계량 분석을 수행하시오.
# 
# 조건1) 명목 척도 변수인 학교 유형(type), 합격 여부(pass) 변수에 대해 빈도 분석을 수행하고 결과를 
# 막대 그래프와 파이 차트로 시각화하세요.
# 
head(descData)
summary(descData)
tableType <- table(descData$type)
tablePass <- table(descData$pass)

barplot(tableType, col = rainbow(nrow(tableType)), main = '학교 유형 빈도수 그래프')
pie(tableType, col = rainbow(nrow(tableType)), main = '학교 유형 빈도수 그래프')

barplot(tablePass, col = rainbow(nrow(tablePass)), main = '합격 여부 빈도수 그래프')
pie(tablePass, col = rainbow(nrow(tablePass)), main = '합격 여부 빈도수 그래프')

# 조건2) 비율 척도 변수인 나이(age) 변수에 대해 요약치(평균, 표준 편차 등)을 구하시오.
# 
avgAge <- mean(descData$age)
sdAge <- sd(descData$age)

# 조건3) 나이 변수(age)에 대해 다음과 같은 함수(이름 : show_info)를 구현하시오.
# 실행 예시
# show_info(age)
# 
# 구현할 내용
# 왜도 통계량을 출력한다.
# 첨도 통계량을 출력한다.
# 히스토그램을 작성한다.
# 비대칭도 통계량을 설명한다.
# 밀도 분포 곡선을 그린다.
# 정규 분포 곡선도 같이 그려서 정규 분포 검정을 수행한다.
#
showinfo <- function(x1) {
  # 왜도
  cat('왜도 : ', skewness(x1), '\n')
  # 첨도
  cat('첨도 : ', kurtosis(x1), '\n')
  # 히스토그램을 작성한다.
  hist(x1, freq = F)
  # 비대칭도 통계량을 설명한다.
  # 밀도 분포 곡선을 그린다.
  lines(density(x1), col = 'blue')
  # 정규 분포 곡선도 같이 그려서 정규 분포 검정을 수행한다.
  curve(dnorm(x, mean(x1), sd(x1)), col = 'red', add=T)
}

showinfo(descData$age)
```

#### 실습 코드 4 결과물

![fig2](/assets/img/study/r/191029_fig_02.png)