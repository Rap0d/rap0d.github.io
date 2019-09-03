---
layout: post
title: 27. 제네릭 만들기
subtitle: 제네릭 만들기
categories: study
tags: java
---

## Overview

지금까지 JDK에 제네릭으로 구현된 컬렉션을 사용하는 법을 알아보았다.

이 포스트에서는 직접 제네릭 클래스를 만드는 과정을 설명한다.

***

## 제네릭 클래스

제네릭 클래스 또는 인터페이스를 선언하는 방법은 기존의 클래스나 인터페이스 선언 방법과 유사한데, 클래스나 인터페이스 이름 다음에 일반화된 타입(generic type)의 타입 매개 변수를 '&lt;'와 '&gt;' 사이에 추가한다는 차이가 있다.

제네릭 클래스를 작성하는 예를 살펴보자.

### 제네릭 클래스 작성

타입 매개 변수 T를 가진 제네릭 클래스 MyClass는 다음과 같이 선언한다.

```java
public class Myclass<T> {   // 제네릭 클래스 MyClass, 타입 매개 변수 T
    T val;                  // 변수 val의 타입은 T
    void set(T a) {
        val = a;            // T 타입의 값 a를 val에 지정
    }
    T get() {
        return val;         // T 타입의 값 val 리턴
    }
}
```

### 제네릭 클래스 레퍼런스 변수 선언

제네릭 클래스의 레퍼런스 변수를 선언할 때는 다음과 같이 타입 매개 변수에 구체적인 타입을 적는다.

```java
MyClass<String> s;  // <T>에 String으로 구체화
List<Integer> li;   // <E>에 Integer으로 구체화
Vector<String> vs;  // <E>에 String으로 구체화
```

### 제네릭 객체 생성 - 구체화(specialization)

제네릭 타입을 가진 제네릭 클래스에 구체적인 타입을 대입하여, 구체적인 행위를 할 수 있는 객체를 생성하는 과정을 구체화라고 부른다.

이 과정은 자바 컴파일러에 의해 이루어진다.

`MyClass<T>`에서 T에 구체적인 타입을 지정하여 객체를 생성하는 예는 다음과 같다.

```java
MyClass<String> s = new MyClass<String>();      // 제네릭 타입 T에 String을 지정함
s.set("hello");
System.out.println(s.get());                    // hello 출력

MyClass<Integer> s = new MyClass<Integer>();    // 제네릭 타입 T에 Integer를 지정함
s.set(5);
System.out.println(s.get());                    // 5 출력
```

`MyClass<String>`은 String만 다루는 구체적인 클래스가 되며, `MyClass<Integer>`는 정수만 다루는 구체적인 클래스가 된다.

다음 코드는 구체화된 `MyClass<String>`의 코드를 보여주며 객체 s는 다음의 모양을 가진다.

```java
public class MyClass<String> {
    String val;             // 변수 val의 타입은 String
    void set(String a) {
        val = a;            // String 타입의 값 a를 val에 지정
    }
    String get() {
        return val;         // String 타입의 값 val을 리턴
    }
}
```

제네릭은 클래스와 인터페이스에만 적용되므로, 다음과 같이 자바 기본 타입은 사용할 수 없음에 유의해야 한다.

```java
Vector<int> vi = new Vector<int>();         // 컴파일 오류. int는 사용불가
Vector<Integer> vi = new Vector<Integer>();  // 정상 코드
```

### 타입 매개 변수

제네릭 클래스를 작성할 때, 제네릭 클래스 내에서 제네릭 타입을 사용하여 객체를 생성하는 것은 불가하다.

예를 들어 다음 코드는 허용되지 않는다.

```java
public class MyVector<E> { 
    E create () {
        E a = new E();  // 컴파일 오류. 제네릭 타입의 객체 생성 불가
        return a;
    }
}
```

소스 코드에서 타입 매개 변수는 컴파일 할 때까지만 존재하고, 컴파일 된 클래스 파일에는 존재하지 않기 때문에, 실행 시간에 타입 매개 변수가 나타내는 타입의 객체를 만들 수 없다.

### 제네릭 스택 만들기

스택 자료 구조를 제네릭 클래스로 선언하고, String과 Integer형 스택을 사용하는 코드

```java
/**
 * GStack
 */
public class GStack<T> {    // 제네릭 스택 선언, 제네릭 타입 T
    int tos;
    Object[] stck;          // 스택에 요소를 저장할 공간 배열

    public GStack() {
        tos = 0;
        stck = new Object[10];
        // 제네릭 매개 변수로 객체를 생성하거나
        // 배열을 생성할 수 없으므로
        // Object 배열을 생성하여
        // 실제 타입의 객체를 요소로 삽입한다.
    }

    public void push(T item) {
        if (tos == 10) {
            return; // 스택이 꽉 차서
                    // 더 이상 요소를 삽입할 수 없음
        }
        stck[tos] = item;
        tos++;
    }

    public T pop() {
        if (tos == 0)   // 스택이 비어 있어
                        // 꺼낼 요소가 없음
            return null;
        tos--;
        return (T)stck[tos];
        // 타입 매개 변수 타입으로 캐스팅
    }
}

/**
 * MyStack
 */
public class MyStack {

    public static void main(String[] args) {
        GStack<String> stringStack = new GStack<String>();
        // String 타입의 GStack 생성
        stringStack.push("seoul");
        stringStack.push("busan");
        stringStack.push("LA");

        for (int n = 0; n < 3; n++) {
            System.out.println(stringStack.pop());
            // stringStack 스택에 있는 3개의 문자열 pop
        }

        GStack<Integer> intStack = new GStack<Integer>();
        // Integer 타입의 GStack 생성
        intStack.push(1);
        intStack.push(3);
        intStack.push(5);

        for (int n = 0; n < 3; n++) {
            System.out.println(intStack.pop());
            // intStack 스택에 있는 3개의 정수 pop
        }
    }
}
```

#### 실행 결과

```
LA
busan
seoul
5
3
1
```

위의 예제에서 주의할 부분은 `stck = new Object[10]`과 `return (T)stck[tos]`이다.

제네릭 클래스 내에서 제네릭 타입의 객체를 생성할 수 없는 것과 같은 이유로 배열도 생성할 수 없으므로 Object의 배열로 생성하였다.

만일 다음과 같이 쓰면 오류가 발생한다.

```java
stck = new T[10];   // 제네릭에서는 T타입의 배열을 생성할 수 없다. 컴파일 오류
```

배열을 Object의 배열로 선언하였으므로 실제 데이터를 접근하여 사용할 때는 아래와 같이 타입 매개 변수가 나타내는 타입으로 변환해야한다.

```java
return (T)stck[tos];    // 타입 매개 변수 T타입으로 캐스팅
```

***

## 제네릭과 배열