---
layout: post
title: 39. 다중 공선성(Multicollinearity)
subtitle: 다중 공선성(Multicollinearity)
categories: study
tags: rprogramming
---

![r](/assets/img/logo/r-logo.png)

# Overview

**다중 공선성**(Multicollinearity) 문제는 독립 변수간의 강한 상관 관계로 인해 [회귀 분석](https://rap0d.github.io/study/2019/11/06/r_035_regression01/)의 결과를 신뢰할 수 없는 현상을 의미한다.

다중 공선성 문제가 의심이 되는 경우 상관 계수를 구하여 변수간의 강한 상관 관계를 명확히 분석하는 것이 좋다.

변수간의 강한 상관 관계를 갖는 독립 변수를 제거하여 해결할 수 있다.

***

# 다중 공선성 문제 해결

```R
model <- lm(formula=Sepal.Length ~ Sepal.Width + Petal.Length + Petal.Width , data=training) model
vif(model)
# Sepal.Width Petal.Length Petal.Width # 1.218701 14.422474 13.654679
# vif의 값이 10이상이면 다중 공선성 문제 의심 sqrt(vif(model)) > 2
# Sepal.Width Petal.Length Petal.Width # FALSE TRUE TRUE
# 다중 공선성 문제가 의심이 되는 경우 상관 계수를 구하여 변수간의 강한 상관 관계를 명확히 분석하는 것이 좋다. cor(iris[, -5]) # Species(종) 제외
            # Sepal.Length Sepal.Width Petal.Length Petal.Width
# Sepal.Length 1.0000000 -0.1175698 0.8717538 0.8179411
# Sepal.Width -0.1175698 1.0000000 -0.4284401 -0.3661259
# Petal.Length 0.8717538 -0.4284401 1.0000000 0.9628654 <-- 높은 상관 관계
# Petal.Width 0.8179411 -0.3661259 0.9628654 1.0000000
```