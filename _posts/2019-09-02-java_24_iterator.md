---
layout: post
title: 24. 제네릭 컬렉션 활용 - Iterator
subtitle: 제네릭 컬렉션 활용 - Iterator
categories: study
tags: java
---
![javalogo](/assets/img/logo/java-logo.png)
## Overview

[제네릭 컬렉션](https://rap0d.github.io/study/2019/08/29/java_21_collection/)의 Iterator&lt;E&gt;에 대해서 알아본다.

***

## 컬렉션의 순차 검색을 위한 Iterator

Vector, ArrayList, LinkedList, Set과 같은 리스트 모양의 컬렉션에서 요소를 순차적으로 검색할 때는 `java.util` 패키지의 **Iterator&lt;E&gt;** 인터페이스를 사용하면 편리하다.

Iterator&lt;E&gt;에서 &lt;E&gt;에는 검색 대상 컬렉션의 매개 변수 타입과 동일하게 설정하면 된다.

메소드는 다음 표와 같이 간단하지만 매우 강력하다.

| 메소드 | 설명 |
| :---------- | :---------- |
| boolean hasNext() | 다음 반복에서 사용될 요소가 있으면 true 리턴 |
| E Next() | 다음 요소 리턴 |
| void remove() | 마지막으로 리턴된 요소 제거 |

예를 들어 다음과 같은 벡터가 있다고 하자.

```java
Vector<Integer> v = new Vector<Integer>();  // Integer 타입을 요소로 하는 벡터
```

Vector&lt;E&gt;는 상속을 통해 Iterator 인터페이스를 구현하고 있으므로, 다음과 같이 `iterator()` 메소드를 호출하여 Iterator 객체를 얻어낸다.

Iterator 객체를 **반복자**라고 부르기도 한다.

```java
Iterator<Integer> it = v.iterator();    // 벡터 v의 요소를 순차 검색할 Iterator 객체 리턴
```

벡터 `v`의 요소 타입(*Integer*)에 맞추어 Iterator&lt;E&gt;의 &lt;E&gt;에 *Integer*를 지정하였다.

이제, `it` 객체를 이용하면 인덱스 없이 벡터의 각 요소를 순차적으로 검색할 수 있다.

다음은 `it`로 컬렉션의 각 요소들을 방문하는 코드이다.

```java
while (it.hasNext()) {  // it로 모든 요소를 방문할 때까지
    int n = it.next();  // 다음 요소 리턴. it의 요소 타입은 Integer
    ...
}
```

### Iterator를 이용하여 Vector 속의 모든 요소를 출력하고 합 구하기

Vector&lt;Integer&gt;로부터 Iterator를 얻어내고 이를 이용하여 벡터의 모든 정수를 출력하고 합을 구한다.

```java
public class IteratorEx {
    public static void main(String[] args) {
        // 정수 값만 다루는 제네릭 벡터 생성
        Vector<Integer> v = new Vector<Integer>();
        v.add(5);
        v.add(4);
        v.add(-1);
        v.add(2, 100);

        // Iterator를 이용한 모든 정수 출력하기
        Iterator<Integer> it = v.iterator();    // Iterator 객체 얻기
        while(it.hasNext()) {
            int n = it.next();
            System.out.println(n);
        }

        // Iterator를 이용하여 모든 정수 더하기
        int sum = 0;
        it = v.iterator();  // Iterator 객체 얻기
        while(it.hasNext()) {
            int n = it.next();
            sum += n;
        }
        System.out.println("벡터에 있는 정수 합 : " + sum);
    }
}
```

#### 실행 결과

```
5
4
100
-1
벡터에 있는 정수 합 : 108
```