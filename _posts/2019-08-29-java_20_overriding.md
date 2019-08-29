---
layout: post
title: 20. 메소드 오버라이딩
subtitle: 메소드 오버라이딩
categories: study
tags: java
---

## Overview

**메소드 오버라이딩**(method overriding)은 슈퍼 클래스와 서브 클래스의 메소드 사이에 발생하는 관계이며, 슈퍼 클래스의 메소드를 동일한 이름으로 서브 클래스에서 재작성하는 것이다.

***

## 메소드 오버라이딩 정의

**메소드 오버라이딩**은 슈퍼 클래스에 선언된 메소드와 같은 이름, 같은 리턴타입, 같은 매개 변수 리스트를 갖는 메소드를 서브 클래스에서 재작성하는 것이다.

메소드 오버라이딩은 다른 말로 ***'슈퍼 클래스 메소드 무시하기'***로 표현할 수 있다.

이는 슈퍼클래스의 메소드를 무시하고 서브 클래스에서 오버라이딩된 메소드가 무조건 실행되도록 동적 바인딩 되기 때문이다.

다음 그림은 메소드 오버라이딩의 개념을 보여준다.

![슈퍼클래스의 메소드2()를 무시하고 서브 클래스에서 메소드2()를 새로 작성한 메소드 오버라이딩](/assets/img/study/java/190829_fig_2.png "슈퍼클래스의 메소드2()를 무시하고 서브 클래스에서 메소드2()를 새로 작성한 메소드 오버라이딩")

서브 클래스에서 슈퍼 클래스의 *메소드2()*를 무시하고 새로 *메소드2()*를 재작성한 사례이다.

이런 경우 외부에서 서브 클래스 객체의 *메소드2()*를 호출하면 항상 서브 클래스의 *메소드2()*가 호출된다.

다음 코드는 실제 오버라이딩의 한 예이다.

```java
/**
* code_21
* DObject 클래스
*/
class DObject {
    public DObject next;

    public DObject() {next = null;}
    public void draw() {
        System.out.println("DObject draw");
    }
}
```
# &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$$\Uparrow$$
```java
/**
* code_22
* DObject 클래스의 draw() 메소드를 오버라이딩 함
*/
class Line extends DObject {
    public void draw() {
        System.out.println("Line");
    }
}
class Rect extends DObject {
    public void draw() {
        System.out.println("Rect");
    }
}
class Circle extends DObject {
    public void draw() {
        System.out.println("Circle");
    }
}
```

***

## 오버라이딩된 메소드 호출

위 `code_21, 22`의 소스를 이용하여 오버라이딩된 메소드를 호출하는 경우를 살펴보자.

다음 그림은 두 가지 경우를 보여준다.

![오버라이딩에 의해 서브 클래스의 메소드가 호출되는 경우](/assets/img/study/java/190829_fig_3.png "오버라이딩에 의해 서브 클래스의 메소드가 호출되는 경우")

`new Line()`에 의해 `Line` 객체가 생성되면 `draw()` 메소드는 2개가 존재하게 된다.

이때 *1)*의 경우는 `a`가 `Line` 타입의 레퍼런스이므로 생성된 `Line` 객체의 `draw()` 메소드가 호출된다.

*2)*의 경우에서 `p`가 `DObject` 타입이므로 `p.draw()`를 컴파일할 때, 컴파일러는 `p`의 타입인 `DObject`에 `draw()`라는 멤버가 있는지 확인한다.

`DObject`에 `draw()`가 있기 때문에 컴파일이 성공한다.

그러나 문제는 실행되는 순간에 있다.

실행 시 `p`가 가리키는 객체에는 실제 `draw()`가 2개 존재하며 `DObject`의 `draw()`를 오버라이딩한 `Line`의 `draw()`가 존재하기 때문에 `Line`의 `draw()`를 호출한다.

결과적으로 *2)*의 경우에는 `p`가 가리키는 객체에 최종적으로 오버라이딩된 메소드 `draw()`가 호출되는 것이다.

이 과정을 **동적 바인딩**(dynamic binding)이라고 부른다.

> 메소드 오버라이딩은 객체 지향 프로그래밍의 핵심이다.  
> 소프트웨어의 재사용을 위해 구현된 클래스를 상속받아 가져다 쓸 때 개발자의 입맛에 맞게 슈퍼 클래스에 구현된 메소드의 소스 코드를 수정할 수는 없다.  
> 이때 오버라이딩을 통해 슈퍼 클래스와 동일한 이름의 메소드를 재작성하여 슈퍼 클래스에 작성된 메소드를 무시하게 만듦으로서 개발자가 작성한 메소드만이 작동하게 하는 것이다.

***

## 메소드 오버라이딩 만들기

오버라이딩된 메소드를 호출하는 예시를 통해 메소드 오버라이딩을 이해해보자.

```java
class DObject {
    public DObject next;

    public DObject() {next = null;}
    public void draw() {
        System.out.println("DObject draw");
    }
}

class Line extends DObject {
    public void draw() {    // 메소드 오버라이딩
        System.out.println("Line");
    }
}

class Rect extends DObject {
    public void draw() {    // 메소드 오버라이딩
        System.out.println("Rect");
    }
}

class Circle extends DObject {
    public void draw() {    // 메소드 오버라이딩
        System.out.println("Circle");
    }
}

public class MethodOverridingEx {
    public static void main(String[] args) {
        DObject obj = new DObject();
        Line line = new Line();
        DObject p = new Line();
        DObject r = line;

        obj.draw();     // DObject.draw() 메소드 실행. "DObject draw" 출력
        line.draw();    // Line.draw() 메소드 실행. "Line" 출력
        p.draw();       // 오버라이딩된 메소드 Line.draw() 메소드 실행. "Line" 출력
        r.draw();       // 오버라이딩된 메소드 Line.draw() 메소드 실행. "Line" 출력

        DObject rect = new Rect();
        DObject circle = new Circle();
        rect.draw();    // 오버라이딩된 메소드 Rect.draw() 메소드 실행. "Rect" 출력
        circle.draw();  // 오버라이딩된 메소드 Circle.draw() 메소드 실행. "Circle" 출력
    }
}
```

### 실행 결과

```
DObject draw
Line
Line
Line
Rect
Circle
```

***

## 메소드 오버라이딩 조건

메소드 오버라이딩을 작성하는 데는 다음과 같은 몇 가지 제약 조건이 있다.

> **메소드 오버라이딩은 슈퍼 클래스의 메소드와 완전히 동일한 메소드를 재정의한다.**  
> - 슈퍼클래스에 선언된 메소드와 같은 이름, 같은 리턴타입, 같은 매개 변수 리스트를 갖는 메소드를 작성해야 한다.
> 
> **메소드 오버라이딩 시에 슈퍼클래스 메소드의 접근 지정자보다 접근의 범위가 좁아질 수 없다.**  
> - 접근 지정자는 public, protected, default, private순으로 범위가 좁아진다.  
> - 따라서 슈퍼 클래스의 메소드가 public으로 선언되었다면 서브 클래스에서 메소드 오버라이딩 시 protected나 private을 사용할 수 없으며 반드시 public으로 해야한다.  
> - 또한 슈퍼 클래스의 메소드가 protected라면 메소드 오버라이딩 시에 protected나 public만 사용할 수 있다.
>  
> **메소드 오버라이딩에서 메소드 이름, 매개 변수 리스트는 같으나 리턴 타입만 다를 수 없다.**  
> - 만일 Line 클래스를 아래와 같이 작성한다면 오버라이딩이 실패하고 컴파일 오류가 발생한다.  
> ```java
> class Line extends DObject {
>   public int draw() { //리턴타입이 달라 오버라이딩 실패, 컴파일 오류
>       return 5;
>   }
> }
> ```
> 
> **static, private, final로 선언된 메소드는 오버라이딩될 수 없다.**

***

## 메소드 오버라이딩의 활용

메소드 오버라이딩은 서브 클래스를 작성하는 개발자가 상속받은 슈퍼 클래스의 어떤 메소드를 자신의 특성에 맞게 새로 만들어 사용하고 싶은 경우에 활용된다.

메소드 오버라이딩의 활용 예를 들어보자. 위의 code_21에 정의된 클래스를 활용하는 main()메소드를 다음 코드에 작성하였다.

```java
/**
* code_23
*/
public static void main(String[] args) {
    DObject start, n, obj;

    // 링크드 리스트로 도형 생성하여 연결하기
    start = new Line(); // Line 객체 연결
    n = start;
    obj = new Rect();
    n.next = obj;       // Rect 객체 연결
    n = obj;
    obj = new Line();
    n.next = obj;       // Line 객체 연결
    n = obj;
    obj = new Circle();
    n.next = obj;       // Circle 객체 연결

    // 모든 도형 출력하기
    while (start != null) {
        start.draw();
        start = start.next;
    }
}
```

### 실행결과

```
Line
Rect
Line
Circle
```

main() 메소드는 Line, Rect, Line, Circle 객체를 순서대로 생성하여 링크드 리스트로 연결하고 start는 처음 객체를 가리킨다.

각 객체에는 두 개의 draw() 메소드가 존재한다.

main() 메소드는 다음과 같이 start 레퍼런스에 연결된 모든 도형 객체를 방문하면서 draw() 메소드를 호출한다.

```java
while (start != null) {
    start.draw();       // start가 가리키는 객체의 오버라이딩된 draw() 메소드 호출
    start = start.next; // start는 다음 객체의 레퍼런스 값을 가짐
}
```

start 레퍼런스의 타입이 DObject 타입이므로 start.draw()는 각 객체의 DObject의 멤버 draw()를 호출하게 될 것 같지만, 실제 각 객체에서 오버라이딩한 draw() 메소드가 호출된다.

그림에서 빨간색 점선 화살표로 표시된 것은 동적 바인딩에 의해 타원으로 둘러싼 draw()가 실행됨을 표시한다.

***

## 동적 바인딩 : 오버라이딩된 메소드가 항상 우선적으로 호출된다.

