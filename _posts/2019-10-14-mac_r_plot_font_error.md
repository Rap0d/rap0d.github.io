---
layout: post
title: macOS환경의 R에서 plots을 사용할 때 한글 깨짐 문제 해결
subtitle: macOS환경의 R에서 plots을 사용할 때 한글 깨짐 문제 해결
categories: tip
tags: mactip
---

![r](/assets/img/logo/r-logo.png)

## Overview

macOS환경의 R에서 plots을 사용할 때 한글 폰트가 깨지는 문제를 해결하는 문서

***

## 사용된 환경

> OS : macOS Catalina 10.15(19A583)  
> R : 3.6.1  
> R Studio : 1.2.5001  
> 기준 일자 : 2019-10-14 

***

## 이슈

R Studio에서 plots을 사용하려 할 때, 한글 폰트가 네모칸으로 깨지는 문제

![error](/assets/img/tip/mactip/r/191015_fig_01.png)

***

## 해결

plot의 옵션을 주는 `par`함수의 옵션인 `family`를 사용하여 한글 폰트를 지정해준다.

```R
# D2Coding 대신에 사용하고 싶은 폰트 이름
par(family = 'D2Coding')
```

![solution](/assets/img/tip/mactip/r/191015_fig_02.png)