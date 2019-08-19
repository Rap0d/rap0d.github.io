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

1번 경우인 상속관계를 예시로 설명하겠다.

`class Parent`, `class Child extends Parent `클래스가 있다고 가정하자.

Child는 Parent 클래스를 상속 받으므로 Child 클래스가 Parent 클래스보다 가지고 있는 데이터 양이 무조간 많다.

그 이유는 Child 클래스는 적어도 Parent 클래스의 데이터를 가지고 있기 때문이다.

# &nbsp;&nbsp;&nbsp;&nbsp;Parent
# &nbsp;&nbsp;&nbsp;&nbsp;
# &nbsp;&nbsp;&nbsp;&nbsp;Child

때문에 많은 사람들이 두 클래스를 표현할때 위와 같이 한다.

사람마다 틀리겠지만, 나중에 업캐스팅, 다운캐스팅의 개념을 위해 위와 같이 알아 놓아야 한다.

클래스 데이터 즉, 참조형을 사용할때는 아래와 같이 쓴다.

```java
Parent parent = new Parent();
```

Parent 데이터형인 `parent` 변수는 실제 데이터인 `new Parent();`란 인스턴스가 Parent 정보를 모두 가지고 있으므로 오류가 나지 않는다.

아래의 경우는 어떨까?

```java
Parent parent = new Child();
```

위 개념으로 생각해보자.

`parent` 변수는 Parent 자료형 데이터 모두를 원한다.

`new Child();`라는 인스턴스는 Parent 자료형 데이터를 모두 가지고 있을까?

대답은 **Yes**이다. 왜냐하면 Child 클래스는 Parent 클래스를 상속받았기 때문에 성립한다.

엄밀히 말하자면 데이터형은 다르므로 아래와 같이 형변환 기호를 붙여야 한다.

```java
Parent parent = (Parent) new Child();
```

하지만 위에서 언급했다시피 변수가 원하는 정보를 인스턴스가 모두 가지고 있으므로, 컴파일러가 형변환 기호를 붙이지 않아도 다형성 측면에서 넘어가는 것이다.

위와 같은 캐스팅을 **업캐스팅**이라 부른다.

왜냐하면 Parent 데이터형에 Child형 데이터를 넣어 생기는 아래와 같은 구식도 때문이다.

# &nbsp;&nbsp;&nbsp;&nbsp;Parent
# &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$$\Uparrow$$
# &nbsp;&nbsp;&nbsp;&nbsp;Child

Parent에 Child 데이터를 넣으므로 화살표 방향이 위로 향하게 된다.

이런식으로 구조를 이해한다면 업캐스팅인 경우를 외우지 않아도 가능할 것이다.

다음 포스팅에서는 [다운캐스팅](https://rap0d.github.io/study/2019/08/19/java_11_java_downcasting/)에 대해 다룬다.

***

### 참고 문서
- [UpCasting](https://mommoo.tistory.com/41)
