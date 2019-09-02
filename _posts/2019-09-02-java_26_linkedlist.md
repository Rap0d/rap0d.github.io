---
layout: post
title: 26. 제네릭 컬렉션 활용 - LinkedList와 Collections 클래스
subtitle: 제네릭 컬렉션 활용 - LinkedList와 Collections 클래스
categories: study
tags: java
---

## Overview

[제네릭 컬렉션](https://rap0d.github.io/study/2019/08/29/java_21_collection/)의 LinkedList&lt;E&gt;와 Collections클래스에 대해서 알아본다.

***

## LinkedList&lt;E&gt;

**LinkedList&lt;E&gt;**는 컬렉션 인터페이스인 List 인터페이스를 구현한 클래스로서 경로명이 `java.util.LinkedList`이다.

LinkedList는 요소들이 양방향으로 연결하여 관리한다는 점을 제외하고 Vector, ArrayList와 매우 유사하다.

다음 그림은 `add()` 메소드와 `get()` 메소드를 가진 LinkedList 객체의 내부 구성을 보여준다.

![LinkedList의 내부 구성과 add(), get() 메소드](/assets/img/study/java/190902_fig_2.png "LinkedList의 내부 구성과 add(), get() 메소드")

LinkedList는 맨 앞과 맨 뒤를 가리키는 **head, tail** 레퍼런스를 가지고 있어, 맨 앞이나 맨 뒤, 중간에 요소의 삽입이 가능하며 인덱스를 이용하여 요소에 접근할 수도 있다.

***

## Collections 클래스 활용

`java.util` 패키지에 포함된 Collection 클래스는 컬렉션을 다루기 위한 다음과 같은 유용한 유틸리티 메소드를 지원한다.

> `sort()` - 컬렉션에 포함된 요소들의 정렬  
> `reverse()` - 요소를 반대 순으로 정렬  
> `max(), min()` - 요소들의 최댓값과 최솟값 찾아내기  
> `binarySearch()` - 이진 검색  

Collections 클래스의 메소드는 모두 `static` 타입이므로 Collections 객체를 생성할 필요는 없다.

이 유틸리티 메소드들은 인자로 컬렉션 객체를 전달받아 처리한다.

다음 예제는 Collections의 유틸리티 메소드의 사용을 보여준다.

```java
public class CollectionsEx {
    static void printList(LinkedList<String> l) {
        // 리스트의 요소를 모두 출력하는 메소드
        Iterator<String> iterator = l.iterator();
        // Iterator 객체 리턴
        while (iterator.hasNext()) {
            // Iterator 객체에 요소가 있을 때까지 반복
            String e = iterator.next(); // 다음 요소 리턴
            String seperator;
            if (iterator.hasNext())
                seperator = "->";       // 마지막 요소가 아니면 -> 출력
            else
                seperator = "\n";       // 마지막 요소이면 줄바꿈
            System.out.print(e + seperator);
        }
    }

    public static void main(String[] args) {
        LinkedList<String> myList = new LinkedList<String>();
        myList.add("트랜스포머");
        myList.add("스타워즈");
        myList.add("매트릭스");
        myList.add(0, "터미네이터");
        myList.add(2, "아바타");
        
        // static 메소드이므로 클래스 이름으로 바로 호출
        Collections.sort(myList);       // 요소 정렬
        printList(myList);              // 요소 출력

        Collections.reverse(myList);    // 요소 순서 반대로 구성
        printList(myList);

        // 아바타의 인덱스 검색
        int index = Collections.binarySearch(myList, "아바타") + 1;
        System.out.println("아바타는 " + index + "번째 요소입니다.");
    }
}
```

#### 검색 결과

```
매트릭스->스타워즈->아바타->터미네이터->트랜스포머
트랜스포머->터미네이터->아바타->스타워즈->매트릭스
아바타는 3번째 요소입니다.
```

### 주의

Collections 클래스는 `java.lang.Comparable`을 상속받는 *element*에 대해서만 작동한다.

*int, char, double*의 기본 타입과 *String* 클래스는 이 조건을 충족하지만, 따로 사용자가 클래스를 작성하는 경우 `java.lang.Comparable`을 상속받아야 한다.