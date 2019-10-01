---
layout: post
title: 20. 메소드 오버라이딩
subtitle: 메소드 오버라이딩
categories: study
tags: java
---
![javalogo](/assets/img/logo/java-logo.png)
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
> - 접근 지정자는 *public, protected, default, private*순으로 범위가 좁아진다.  
> - 따라서 슈퍼 클래스의 메소드가 *public*으로 선언되었다면 서브 클래스에서 메소드 오버라이딩 시 *protected*나 *private*을 사용할 수 없으며 반드시 *public*으로 해야한다.  
> - 또한 슈퍼 클래스의 메소드가 *protected*라면 메소드 오버라이딩 시에 *protected*나 *public*만 사용할 수 있다.
>  
> **메소드 오버라이딩에서 메소드 이름, 매개 변수 리스트는 같으나 리턴 타입만 다를 수 없다.**  
> - 만일 `Line` 클래스를 아래와 같이 작성한다면 오버라이딩이 실패하고 컴파일 오류가 발생한다.  
> 
> ```java
> class Line extends DObject {
>   public int draw() { //리턴타입이 달라 오버라이딩 실패, 컴파일 오류
>       return 5;
>   }
> }
> ```
> 
> ***static, private, final*로 선언된 메소드는 오버라이딩될 수 없다.**

***

## 메소드 오버라이딩의 활용

메소드 오버라이딩은 서브 클래스를 작성하는 개발자가 상속받은 슈퍼 클래스의 어떤 메소드를 자신의 특성에 맞게 새로 만들어 사용하고 싶은 경우에 활용된다.

메소드 오버라이딩의 활용 예를 들어보자. 위의 `code_21`에 정의된 클래스를 활용하는 `main()`메소드를 다음 코드에 작성하였다.

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

`main()` 메소드는 `Line, Rect, Line, Circle` 객체를 순서대로 생성하여 링크드 리스트로 연결하고 `start`는 처음 객체를 가리킨다.

각 객체에는 두 개의 `draw()` 메소드가 존재한다.

`main()` 메소드는 다음과 같이 `start` 레퍼런스에 연결된 모든 도형 객체를 방문하면서 `draw()` 메소드를 호출한다.

```java
while (start != null) {
    start.draw();       // start가 가리키는 객체의 오버라이딩된 draw() 메소드 호출
    start = start.next; // start는 다음 객체의 레퍼런스 값을 가짐
}
```

`start` 레퍼런스의 타입이 `DObject` 타입이므로 `start.draw()`는 각 객체의 `DObject`의 멤버 `draw()`를 호출하게 될 것 같지만, 실제 각 객체에서 오버라이딩한 `draw()` 메소드가 호출된다.

그림에서 빨간색 점선 화살표로 표시된 것은 동적 바인딩에 의해 타원으로 둘러싼 `draw()`가 실행됨을 표시한다.

***

## 동적 바인딩 : 오버라이딩된 메소드가 항상 우선적으로 호출된다.

```java
/**
* code_24
* 오버라이딩된 메소드를 호출하는 동적 바인딩
*/
public class SuperObject {
    protected String name;
    public void paint() {
        draw();
    }
    public void draw() {
        System.out.println("Super Object");
    }
    public static void main(String[] args) {
        SuperObject a = new SuperObject();
        a.paint();
    }
}

/**
* code_25
*/
class SuperObject {
    protected String name;
    public void paint() {
        draw();
    }
    public void draw() {        // 이 메소드를 호출하지 않음
        System.out.println("Super Object");
    }
    public class SubObject extends SuperObject {
        public void draw() {    // 이 메소드를 호출함 (동적 바인딩)
            System.out.println("Sub Object");
        }
        public static void main(String[] args) {
            SuperObject b = new SubObject();
            b.paint();
        }
    }
}
```

### code_24 실행결과

```
Super Object
```

### code_25 실행결과

```
Sub Object
```

위 코드는 두 개의 사례를 보여준다.

24번 코드는 `SuperObject` 클래스 하나만 가진 응용프로그램이며 `SuperObject` 클래스는 `draw()` 메소드를 하나만 가지고 있다.

그러므로 다음과 같이 `main()`에서 `a.paint()`  메소드를 호출하면 `SuperObject` 클래스의 `draw()`가 자연스럽게 호출된다.

```java
SuperObject a = new SuperObject();
a.paint();  // paint()는 SuperObject의 draw()를 호출한다.
```

그러면 25번 코드를 보자.

`SuperObject`와 이를 상속받는 `SubObject`가 있고 `SubObject`에서는 `draw()` 메소드를 오버라이딩하고 있다.

`main()`에서 다음과 같이 `b.paint()`를 호출하면 객체 b에는 `draw()`가 두 개 존재하므로 `paint()`는 두 개의 메소드 중에서 어떤 것을 호출할지 결정하는 동적 바인딩의 과정을 거친다.

```java
SuperObject b = new SubObject();
b.paint();  // paint()는 SubObject에서 오버라이딩한 draw()를 호출한다.
```

그 결과 paint() 메소드는 `SuperObject`의 멤버인 `draw()`가 오버라이딩된 메소드임을 발견하고 `SubObject`의 멤버 `draw()`를 호출한다.

결국 `SuperObject`이든 `SubObject`이든 `draw()`를 호출하면 항상 동적 바인딩이 일어나서 오버라이딩한 SubObject의 `draw()`가 호출된다.

**동적 바인딩**(dynamic binding)은 실행할 메소드를 **컴파일 시**(compile time)가 아니라 **실행 시**(run time)에 결정하는 것을 말한다.

어떤 경우이든 자바에서 오버라이딩된 메소드가 있다면 동적 바인딩을 통해 우선적으로 실행된다.

***

## 오버라이딩과 super 키워드

앞에서 설명한 내용은 메소드가 오버라이딩되어 있을 때 슈퍼 클래스의 메소드가 호출되면 동적 바인딩에 의해 항상 서브 클래스에 오버라이딩된 메소드가 호출된다는 것이다.

그러면 오버라이딩된 슈퍼 클래스의 메소드는 이제 더 이상 쓸모없게 된 것일까?

슈퍼 클래스의 메소드를 호출하려면 다음과 같이 서브 클래스에서 **super** 키워드를 이용하면 슈퍼 클래스의 멤버에 접근할 수 있다.

```
super.슈퍼클래스의멤버
```

*super*는 자바에서 자동으로 지원되는 것으로 **슈퍼 클레스에 대한 레퍼런스**이다.

super로 필드와 메소드 모두 접근 가능하다.

다음 코드의 사례를 살펴본다.

```java
class SuperObject {
    protected String name;
    public void paint() {
        draw();
    }
    public void draw() {
        System.out.println(name);
    }
}
public class SubObject extends SuperObject {
    protected String name;
    public void draw() {
        name = "Sub";
        super.name = "Super";
        super.draw();
        System.out.println(name);
    }
    public static void main(String[] args) {
        SuperObject b = new SubObject();
        b.paint();
    }
}
```

### 실행 결과
```
Super
Sub
```

`SubObject` 클래스의 `draw()`에 구현된 다음 코드를 보자.

```java
name = "Sub";           // SubObject 클래스의 필드 name에 접근
super.name = "Super";   // SuperObject 클래스의 name에 접근
super.draw();           // SuperObject 클래스의 draw() 메소드 호출
```

이 응용프로그램의 결과의 두 개 `name` 필드에 각각 다른 스트링이 지정되어 있는 것을 볼 수 있다.

***

## this, this(), super, super()의 사용

**this**와 **super**는 모두 레퍼런스로서 *this*는 현재 객체의 주소(혹은 레퍼런스)를, *super*는 현재 객체 내에 있는 슈퍼 클래스 영역의 주소를 가진다.

그러므로 다음과 같이 멤버에 접근한다.

```
this.객체내의 멤버
super.객체내의 슈퍼클래스의 멤버
```

한편 **this()**와 **super()**는 모두 메소드 호출이며, *this()*는 생성자에서 동일한 클래스 내의 다른 생성자를 호출할 때 사용하고, *super()*는 서브 클래스의 생성자에서 슈퍼 클래스의 생성자를 선택 호출할 때 사용한다.

***

## 메소드 오버라이딩 예제

`Person`을 상속받는 `Professor`라는 새로운 클래스를 만들고 `Professor` 클래스에서 `getPhone()` 메소드를 오버라이딩하고 이 메소드에서 슈퍼 클래스의 `getPhone()` 메소드를 호출한다.

```java
class Person {
    String phone;

    public void setPhone(String phone) {
        this.phone = phone;
    }
    public String getPhone() {
        return phone;
    }
}

class Professor extends Person {
    public String getPhone() {                      // Person의 getPhone()을 오버라이딩
        return "Professor : " + super.getPhone();   // Person의 getPhone() 호출
    }
}

public class Overriding {
    public static void main(String[] args) {
        Professor a = new Professor();
        a.setPhone("011-123-1234");         // Professor의 getPhone() 호출
        System.out.println(a.getPhone());

        Person p = a;
        System.out.println(p.getPhone());   // 동적 바인딩에 의해 Professor의 getPhone() 호출
    }
}
```

### 실행 결과

```
Professor : 011-123-1234
Professor : 011-123-1234
```

***

## 오버로딩(overloading)과 오버라이딩(overriding)

메소드 **오버라이딩**은 슈퍼 클래스의 메소드와 이름, 매개 변수 타입, 매개 변수 리스트, 리턴 타입 등이 모두 동일한 메소드가 서브 클래스에 재정의되었을 경우를 가리키는 용어이며, **오버로딩**은 한 클래스나 상속 관계에 있는 클래스들에서 서로 인자의 타입이 다르거나 인자의 개수가 다른 여러 개의 동일한 이름의 메소드가 작성되는 것을 지칭한다.

메소드 오버라이딩은 반드시 상속 관계에서만 성립되지만 오버로딩은 동일한 클래스 내 혹은 상속 관계 둘 다 가능하다.

이 둘의 차이점을 다음 표에 정리하였다.

![메소드 오버로딩과 오버라이딩의 차이점](/assets/img/study/java/190829_fig_4.png "메소드 오버로딩과 오버라이딩의 차이점")