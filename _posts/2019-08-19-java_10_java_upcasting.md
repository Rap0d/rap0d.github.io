---
layout: post
title: 10. Java UpCasting(업캐스팅)
subtitle: Java UpCasting(업캐스팅)
categories: study
tags: study java
---

## Overview

[이전 포스팅](https://rap0d.github.io/study/2019/08/18/java_9_java_casting/)에서 **Casting**(형변환)에 대해 다루었다.

이전 포스팅 내용을 요약하면 다음과 같다.

> 자료형이 정해진 변수에 값을 넣을때는  
> 변수가 원하는 정보를 빠짐없이 다 넣어줘야 성립한다.

이전 포스팅은 기본형으로 캐스팅을 설명했다면, 이 포스팅에서는 참조형으로 캐스팅을 설명한다.

***

### UpCasting

기본적으로 캐스팅은 서로 관련 없는 데이터끼리는 변환되지 않는다.

```java
int a = (int)true;
```

boolean 자료형과 int 자료형은 서로 성질이 맞지 않는 데이터 이므로 형변환이 되지 않는다.

참조형(Java에서는 클래스) 데이터 역시 마찬가지다.

그렇다면 참조형 데이터가 서로 관련이 있다고 하는 것은 무엇을 의미하는 것일까?

아래와 같은 상황을 보자.

> 1. 상속관계가 맺어진 경우  
> 2. 인터페이스로 인해 확장이 된 경우

1번 경우인 상속솬계를 예시로 설명하겠다.

`class Parent`, `class Child extends Parent `클래스가 있다고 가정하자.

Child는 Parent 클래스를 상속 받으므로 Child 클래스가 Parent 클래스보다 가지고 있는 데이터 양이 무조간 많다.

그 이유는 Child 클래스는 적어도 Parent 클래스의 데이터를 가지고 있기 때문이다.

# Parent
# &nbsp;
# Child

때문에 많은 사람들이 두 클래스를 표현할때 위와 같이 한다.

사람마다 틀리겠지만, 나중에 업캐스팅, 다운캐스팅의 개념을 위해 위와 같이 알아 놓아야 한다.


***

### 참고 문서
- [UpCasting](https://mommoo.tistory.com/41)
