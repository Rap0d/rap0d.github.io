---
layout: post
title: 11. Java DownCasting(다운캐스팅)
subtitle: Java DownCasting(다운캐스팅)
categories: study
tags: java
---

## Overview

앞 포스팅에서 [형변환](https://rap0d.github.io/study/2019/08/18/java_9_java_casting/)과 [업캐스팅](https://rap0d.github.io/study/2019/08/19/java_10_java_upcasting/)에 대해 다루었다.

이번에는 마지막 차례인 **DownCasting**(다운캐스팅)에 대해 알아본다.

***

### DownCasting

이전 업캐스팅 내용을 복습하자면 자바에서는 관련있는 데이터끼리 형변환이 가능했었다.

ex) Child 클래스가 Parent 클래스를 상속받은 경우

```java
Parent parent = new Child();
```

위 경우는 업캐스팅이라 했었고 형변환 기호(Parent)를 붙여주지 않고 생략을 할 수 잇었다.

그렇다면 반대의 경우는 어떻게 될까?

```java
Child child = new Parent();
```

앞서 포스팅에서 기술한 것처럼 관련있는 데이터끼리 캐스팅이 가능하다고 했지만 위의 경우는 성립하지 않는다.

왜냐하면 변수가 원하는 정보를 다 채워줘야 하는 원칙에 어긋나기 때문이다.

Child 클래스는 Parent 클래스를 상속받았기 때문에 Parent 클래스보다는 Child 클래스가 더욱 많은 데이터를 가졌을 것이다.

즉, Child 변수가 원하는 정보는 Child 클래스의 데이터 전부를 원하는데, Parent 인스턴스 `new Parent();`는 Parent 데이터만 가지고 있을 뿐, Child의 데이터를 가지지 않는다.

그러므로 컴파일 오류가 발생한다.

그렇다면, 위의 경우에서 형변환을 시켜주면 어떨까?

```java
Child child = (Child)new Parent();
```

개발툴에서 확인해보면 컴파일 오류에서 벗어나면서 빨간줄이 사라질 것이다.

하지만 저 코드는 런타임 오류가 발생한다.

이유는 아래와 같다.

> 컴파일러에게 프로그래머가 형변환을 함으로써 데이터를 맞게 넣어준것 처럼 보여준다.  
> 컴파일러는 문법이 맞다고 생각하여 넘어간다.  
> 하지만, 프로그램이 실제로 동작할때 `new Parent();` 인스턴스는 Child 형 데이터로 바꾸지 못한다는 것을 깨닫고, 런타임 오류와 함께 프로그램이 종료된다.

그렇다면 왜 런타임 오류가 발생할까?

그 이유는 JVM은 `new Parent();` 인스턴스를 Child 데이터로 형변환 하려 했지만 Child 데이터가 무엇인지 모르기 때문이다.

구체적인 이유는 Child 데이터는 만드는 프로그래머마다 성질이 다른데 그것을 JVM이 추리하지 못하기 때문이다.

이전 캐스팅 포스팅에서 나오길, 기본 자료형끼리는 추리가 가능하기 때문에 알아서 알맞은 데이터 크기로 변환시켜 준다고 설명했다.

하지만 위와 같이 참조형 데이터를 캐스팅할 때는 속성, 성질이 정해져 있지 않은 참조형 데이터를 JVM이 알 수 없다.

Child 데이터에 Parent 데이터를 넣는 경우는 화살표가 아래로 향하므로 다운캐스팅이라 한다.

# &nbsp;&nbsp;&nbsp;&nbsp;Parent
# &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$$\Downarrow$$
# &nbsp;&nbsp;&nbsp;&nbsp;Child

위의 예시와 같이 다운캐스팅은 일반적으로 성립하지 않는다.

하지만 다운캐스팅이 성립하는 경우가 있다.

아래의 예시를 보자.

```java
Parent parent = new Child();
Child child = (Child)parent;
```

위의 예시는 다운캐스팅이 성립되는 경우의 수이다.

왜 성립이 되는것일까?

parent 변수는 Parent 클래스 형태의 변수지만, 태생이 Child 인스턴스인 데이터를 넣어주었다.

그러한 정보를 가지고 있는 parent 변수를 다시 Child 클래스 형태로 다운캐스팅을 하였다.

parent 변수 상태는 Parent 클래스형 상태지만 다운캐스팅을 해주는 경우 `(Child)parent` Child 클래스형이므로, JVM이 parent 변수를 태생 정보인 Child 클래스의 데이터형으로 다운캐스팅을 해줄 수 있는 것이다.

결과적으로 다운캐스팅은 보통 성립하지 않는 문법이지만, 위와 같이 업캐스팅이 선행된 경우 다운캐스팅이 성립되는 경우가 존재한다.

***

### 참고 문서
- [DownCasting](https://mommoo.tistory.com/51)
