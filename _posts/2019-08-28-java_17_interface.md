---
layout: post
title: 17. Interface
subtitle: Interface
categories: study
tags: java
---

## Overview

자바에서의 인터페이스 개념은 추상 클래스와 유사하며 **interface** 키워드를 사용하여 선언한다.

추상 클래스와의 차이점은 추상클래스는 메소드가 일부 구현을 할 수 있지만 인터페이스의 경우 모두 **선언만 할 수 있다.**

다음은 `Clock`과 `Car` 인터페이스를 선언하는 예이다.

```java
public interface Clock {
    public static final int ONEDAY = 24;    // 상수 필드 선언
    abstract public int getMinute();
    abstract public int getHour();
    abstract void setMinute(int i);
    abstract void setHour(int i);   
}

public interface Car {
    int MAXIMUM_SPEED = 260;    // 상수 필드 선언
    int moveHandle(int degree); // public 생략 가능
    int changeGear(int gear);   // abstract 생략 가능
}
```

***

## 인터페이스의 특징

### 멤버는 추상 메소드와 상수만으로 구성된다.

인터페이스는 메소드와 상수만을 가지며 필드는 가지지 않는다.

메소드는 모두 추상 메소드이다. 그러나 메소드 선언 시 *abstract* 키워드를 생략할 수 있다.

### 모든 메소드는 *public*이며 생략이 가능하다.

모든 메소드는 *public* 접근 지정자이다. 주로 생략하여 사용한다.

### 상수도 *public static final*을 생략하여 선언할 수 있다.

상수는 *public static final* 속성이며 생략 가능핟.

앞의 `Clock` 인터페이스에서 `ONEDAY` 상수는 다음과 같이 사용해도 무관하다.

```java
int ONEDAY = 24;
```

### 인터페이스의 객체를 생성할 수 없다.

인터페이스는 추상 메소드만 가지기 때문에 원칙적으로 객체를 생성할 수 없다.

```java
new Clock();    // 오류. 인터페이스의 객체를 생성할 수 없다.
new Car();      // 오류. 인터페이스의 객체를 생성할 수 없다.
```

### 다른 인터페이스에 상속될 수 있다.

인터페이스는 다른 인터페이스를 상속할 수 있다.

### 인터페이스도 레퍼런스 변수의 타입으로 사용 가능하다.

인터페이스의 레퍼런스 변수를 만드는 것이 가능하다.

```java
Clock clock;    // 인터페이스 레퍼런스 변수
Car car;        // 인터페이스 레퍼런스 변수
```

***

## 인터페이스 상속

클래스는 인터페이스를 상속받을 수 없고 오직 인터페이스만이 상속이 가능하다.

인터페이스는 규격과 같은 것이라 기존 인터페이스를 바꾸는 것은 인터페이스에 맞춰 이미 작성된 소프트웨어를 모두 못쓰게 만들므로 인터페이스에 수정을 가할때는 기존 인터페이스를 상속받아 새로운 인터페이스를 만든다.

자바에서 클래스의 다중 상속은 지원하지 않지만 인터페이스의 다중 상속은 지원한다.

자바에서 인터페이스의 상속 시 *extends* 키워드를 사용한다.

다음은 다중 인터페이스 상속의 예를 보여준다.

```java
interface MobilePhone {
    public boolean sendCall();
    public boolean receiveCall();
    public boolean sendSMS();
    public boolean receiveSMS();
}

interface MP3 {
    public void play();
    public void stop();
}

interface MusicPhone extends MobilePhone, MP3 { // 인터페이스 다중 상속
    public void playMP3RingTone();
}
```

`MusicPhone`은 `MobilePhone`과 `MP3` 인터페이스를 상속받으며 `playMP3RingTone()`이라는 메소드를 추가한다.

***

## 인터페이스 구현

인터페이스 구현이란 인터페이스의 추상 메소드를 클래스에서 구현하는 것을 말한다.

다음과 같이 ***implements***라는 키워드를 사용하여 클래스를 작성한다.

```java
interface USBMouseInterface {
    void mouseMove();
    void mouseClick();
}

public class MouseDriver implements USBMouseInterface {   // 인터페이스 구현. 클래스 작성
    void mouseClick() {...}
    void mouseMove() {...}  // USBMouseInterface의 두 메소드를 모두 구현하였다.

    // 추가적으로 다른 메소드를 작성할 수 있다.
    int getStatus() {...}
    int getButton() {...}
}
```

이때 클래스는 반드시 인터페이스의 모든 추상 메소드를 구현하여야 한다.

아니면 작성된 클래스가 추상 메소드를 상속받아 포함하게 되므로 컴파일 오류가 발생한다.

앞의 소스에서 `MouseDriver` 클래스는 `USBMouseInterface`에 정의된 2개의 추상 메소드를 모두 구현하였다.

그리고 필요에 의해 2개의 메소드를 추가적으로 구현하였다.

***

## 인터페이스의 다중 구현

클래스는 하나 이상의 인터페이스 구현이 가능하며, 여러 개의 인터페이스를 구현할 때는 콤마로 인터페이스를 구분하여 나열하며, 각 인터페이스에 정의된 **모든 추상 메소드를 구현**하여야 한다.

```java
interface USBMouseInterface {
    void mouseMove();
    void mouseClick()'
}

interface RollMouseInterface {
    void roll();
}

// 콤마(,)로 분리하여 상속받는 인터페이스 나열
public class MouseDriver implements RollMouseInterface, USBMouseInterface {
    void mouseMove() {...}
    void mouseClick() {...} // 2개의 인터페이스에 정의된 모든 메소드 구현
    void roll() {...}

    // 추가적으로 다른 메소드를 작성할 수 있다.
    int getStatus() {...}
    int getButton() {...}
}
```

***

## 클래스 상속과 함께 인터페이스 구현

클래스는 슈퍼 클래스의 상속과 동시에 인터페이스를 구현하는 것도 가능하다.

다중 상속, 다중 인터페이스 구현은 유용하나 자칫 너무 남용하면 클래스, 인터페이스 간의 관계가 너무 복잡해져 프로그램 전체 구조를 파악하기 어려울 수 있으므로 주의한다.

예를 들면 다음과 같다.

```java
interface MobilePhone {
    public boolean sendCall();
    public boolean receiveCall();
    public boolean sendSMS();
    public boolean receiveSMS();
}

interface MP3 {
    public void play();
    public void stop();
}

class PDA {
    public int calculate(int x, int y) {
        return x + y;
    }
}

// PDA 클래스를 상속 받고 인터페이스 MobilePhone과 MP3를 구현한다.
public class SmartPhone extends PDA implements MobilePhone, MP3 {
    // MobilePhone의 모든 추상 메소드 구현
    public boolean sendCall() {
        System.out.println("전화 걸기");
        return true;
    }
    public boolean receiveCall() {
        System.out.println("전화 받기");
        return true;
    }
    public boolean sendSMS() {
        System.out.println("SMS 보내기");
        return true;
    }
    public boolean receiveSMS() {
        System.out.println("SMS 받기");
        return true;
    }
    // MobilePhone의 모든 추상 메소드 구현

    // MP3의 모든 추상 메소드 구현
    public void play() {
        System.out.println("음악 재생");
    }
    public void stop() {
        System.out.println("재생 중지");
    }

    // 메소드 추가 구현
    public void scheduler() {
        System.out.println("일정 관리");
    }
    public void applicationManager() {
        System.out.println("응용프로그램 설치 / 삭제");
    }

    public static void main(String[] args) {
        SmartPhone p = new SmartPhone();
        p.sendCall();
        p.play();
        System.out.println(p.calculate(3, 5));
        p.scheduler();
    }
}
```
- 실행 결과
  ```
  전화 걸기
  음악 재생
  8
  일정 관리
  ```

***

## 인터페이스와 추상 클래스

인터페이스와 추상 클래스는 유사점이 많앋.

이 둘의 차이점을 다음 표에 정리하였다.
