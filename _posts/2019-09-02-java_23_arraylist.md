---
layout: post
title: 23. 제네릭 컬렉션 활용 - ArrayList
subtitle: 제네릭 컬렉션 활용 - ArrayList
categories: study
tags: java
---

![javalogo](/assets/img/logo/java-logo.png)

## Overview 

[제네릭 컬렉션](/study/2019/08/29/java_21_collection/)의 ArrayList&lt;E&gt;에 대해서 알아본다.

***

## ArrayList&lt;E&gt;

**ArrayList&lt;E&gt;**(이하 ArrayList)는 가변 크기의 배열을 구현한 컬렉션 클래스로서 경로명은 `java.util.ArrayList`이며, Vector 클래스와 거의 동일하다.

크게 다른 점은 ArrayList는 스레드 간에 동기화를 지원하지 않기 때문에, 다수의 스레드가 동시에 ArrayList에 요소를 삽입하거나 삭제할 때 충돌이 발생할 소지가 있다.

ArrayList를 이용하려면 멀티스레드의 동기화를 사용자가 직접 구현해야 한다.

ArrayList는 Vector와 마찬가지로 요소를 맨 뒤에 추가하거나 중간에 삽입 가능하며, 삽입이나 삭제 시 중간에 빈 공간이 생기지 않도록 요소들의 위치를 앞뒤로 하나씩 자동으로 이동시킨다.

ArrayList는 내부에 배열을 가지고 있으며 이 배열을 가변적 크기로 관리하고, 인덱스로 요소를 접근하도록 지원하며 인덱스는 0부터 시작한다.

다음 표는 ArrayList 클래스의 주요 메소드를 보여준다.

| 메소드 | 설명 |
| :---------- | :---------- |
| boolean add(E element) | ArrayList의 맨 뒤에 element 추가 |
| void add(int index, E element) | 인덱스 index에 element를 삽입 |
| boolean addAll(Collection&lt;? extends E&gt; c) | 컬렉션 c의 모든 요소를 ArrayList의 맨 뒤에 추가 |
| void clear() | ArrayList의 모든 요소 삭제 |
| boolean contains(Object o) | ArrayList가 지정된 객체 o를 포함하고 있으면 true 리턴 |
| E elementAt(int index) | 인덱스 index의 요소 리턴 |
| E get(int index) | 인텍스 index의 요소 리턴 |
| int indexOf(Object 0) | o와 같은 첫 번째 요소의 인덱스 리턴. 없으면 -1 리턴 |
| boolean isEmpty() | ArrayList가 비어 있으면 true 리턴 |
| E remove(int index) | 인덱스 index의 요소 삭제 |
| boolean remove(Object o) | 객체 o와 같은 첫 번째 요소를 ArrayList에서 삭제 |
| int size() | ArrayList가 포함하는 요소의 개수 리턴 |
| Object[] toArray() | ArrayList의 모든 요소를 포함하는 배열 리턴 |

### ArrayList의 생성

문자열만 다루는 ArrayList를 생성해보자.

```java
ArrayList<String> a = new ArrayList<String>();
```

`a`는 문자열만 삽입하고 검색할 수 있는 ArrayList 객체이다. ArrayList는 스스로 용량을 조절하기 때문에 용량에 대해 신경 쓸 필요가 없다.

### ArrayList에 요소 삽입

현재 `a`에 삽입할 수 있는 요소는 String 타입의 문자열만 가능하다.

`add()` 메소드를 사용하여 다음과 같이 3개의 문자열을 삽입해보자.

```java
a.add("Hello");
a.add("Hi");
a.add("Java");
```

`a`에 문자열이 아닌 객체나 값을 삽입하면 다음과 같이 오류가 발생한다.

```java
a.add(5);
a.add(new Point(3, 5));
```

ArrayList에는 `null`도 삽입할 수 있다.

```java
a.add(null);
```

그러므로 ArrayList를 검색할 때 `null`이 있을 수 있음을 염두에 두어야 한다.


예를 들어, 인덱스 2의 위치에 "Sahni"를 삽입하는 코드는 다음과 같다.

```java
a.add(2, "Sahni");
```

이 코드 실행 전, ArrayList에 들어 있는 요소 개수가 2보다 작으면 예외가 발생한다.

그러지 않으면 "Sahni"를 인덱스 2에 삽입하고 기존의 인덱스 2와 그 뒤에 있는 요소들을 모두 한 자리씩 뒤로 이동시킨다.

### ArrayList 내의 요소 알아내기

`get()`, `elementAt()` 등의 메소드를 이용하면 ArrayList 내의 요소를 알아낼 수 있다.

다음 코드는 인덱스 1의 위치에 있는 요소를 리턴한다.

```java
String str = a.get(1);  // Hi를 리턴한다.
```

여기서, `a`가 String만 다루기 때문에 `get()` 메소드의 리턴 타입은 자동으로 *String*이다.

### ArrayList의 크기 알아내기

ArrayList의 크기란 현재 ArrayList에 들어 있는 요소의 개수를 말한다.

`size()` 메소드를 호출하면 개수를 알아낼 수 있다.

```java
int len = a.size(); // ArrayList에 존재하는 요소의 개수
```


### ArrayList에서 요소 삭제

`remove()` 메소드를 이용하면 ArrayList 내에 임의의 인덱스에 있는 요소를 삭제할 수 있다.

다음 코드는 인덱스 1의 위치에 있는 요소를 삭제한다.

이 결과 뒤에 있는 요소들이 한 자리씩 앞으로 이동한다.

```java
a.remove(1);    // 인덱스 1의 위치에 있는 요소 삭제
```

다음과 같이 객체 레퍼런스를 이용하여 `remove()`를 호출할 수도 있다.

```java
String s = new String("bye");
a.add(s);
...
a.remove(s);
```

ArrayList에 있는 모든 요소를 삭제하려면 다음과 같이 `clear()` 메소드를 호출한다.

```java
a.clear();
```

### 키보드로부터 문자열 입력받아 ArrayList에 넣기

키보드로 문자열을 입력받아 ArrayList에 삽입하고 가장 긴 이름을 출력하는 코드

```java
public class ArrayListEx {
    public static void main(String[] args) {
        // 문자열만 삽입가능한 ArrayList 컬렉션 생성
        ArrayList<String> a = new ArrayList<String>();

        Scanner sc = new Scanner(System.in);

        for (int i = 0; i < 4; i++) {
            System.out.print("이름을 입력하세요>>");
            String s = sc.next();
            a.add(s);   // ArrayList 컬렉션에 삽입
        }

        // ArrayList에 들어 있는 모든 이름 출력
        for (int i = 0; i < a.size(); i++) {
            String name = a.get(i); // ArrayList의 i 번째 문자열 얻어오기
            System.out.println(name + " ");
        }

        // 가장 긴 이름 출력
        int longestIndex = 0;   // 현재 가장 긴 이름이 있는 ArrayList 내의 인덱스
        for (int i = 0; i < a.size(); i++) {
            if(a.get(longestIndex).length() < a.get(i).length())    // 이름 길이 비교
                longestIndex = i;   // i 번째 이름이 더 긴 이름
        }
        System.out.println("\n가장 긴 이름은 : " + a.get(longestIndex));
    }
}
```

#### 실행 결과

```
이름을 입력하세요>>Mike
이름을 입력하세요>>Jane
이름을 입력하세요>>Ashley
이름을 입력하세요>>Helen
Mike Jane Ashley Helen 
가장 긴 이름은 : Ashley
```