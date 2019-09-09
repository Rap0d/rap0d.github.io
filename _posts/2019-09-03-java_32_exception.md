---
layout: post
title: 32. 예외 처리
subtitle: 예외 처리
categories: study
tags: java
---

## Overview

프로그램을 만들다 보면 수없이 많은 에러가 난다.

물론 에러가 나는 이유는 프로그램이 오동작을 하지 않기 위한 자바의 배려이다.

하지만 이러한 에러를 무시하고 싶을 때도 있고, 그 에러에 대한 처리를 하고 싶을 때도 있다.

이번 포스트에서는 에러를 처리하는 방법에 대해 알아본다.

***

## 예외는 언제 발생하는가?

에러를 처리하는 방법을 알기 전에 어떤 상황에서 에러가 나는지 본다.

오타를 쳤을 때 나는 구문 에러 같은 것이 아닌 실제 프로그램에서 잘 발생하는 에러를 보기로 한다.

먼저 없는 파일을 여는 코드이다.

```java
BufferedReader br = new BufferedReader(new FileReader("invalid file"));
br.readLine();
br.close();
```

위 코드를 실행하면 다음과 같은 오류가 발생한다.

```java
Exception in thread "main" java.io.FileNotFoundException: invalid file (지정된 파일을 찾을 수 없습니다)
    at java.io.FileInputStream.open(Native Method)
    at java.io.FileInputStream.<init>(Unknown Source)
    at java.io.FileInputStream.<init>(Unknown Source)
    at java.io.FileReader.<init>(Unknown Source)
    ...
```

위의 예와 같이 없는 파일을 열려고 시도하면 `FileNotFoundException`라는 이름의 예외가 발생하게 된다.

이번에는 0으로 다른 숫자를 나누는 경우이다.

```java
int c = 4 / 0;
```

오류 내용은 다음과 같다.

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at Test.main(Test.java:14)
```

4를 0으로 나우려니까 `ArithmeticException`라는 이름의 예외가 발생한다.

마지막으로 다음 에러를 보자.

이 에러는 정말 빈번하게 일어난다.

```java
int[] a = {1, 2, 3};
System.out.println(a[3]);
```

오류 내용은 다음과 같다.

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 3
    at Test.main(Test.java:17)
```

`a`는 `{1, 2, 3}`이란 배열인데 `a[3]`은 `a` 배열에서 구할 수 없는 값이기 때문에 `ArrayIndexOutOfBoundsException`가 나게 된다.

자바는 이런 예외가 발생하면 프로그램을 중단하고 에러메시지를 보여준다.

***

## 예외 처리하기

유연한 프로그래밍을 위한 예외 처리의 기법에 대해 살펴보자.

다음은 예외 처리를 위한 **try, catch**문의 기본 구조이다.

```java
try {
    ...
} catch(예외1) {
    ...
} catch(예외2) {
    ...
...
}
```

*try*문 안의 수행할 문장들에서 예외가 발생하지 않는다면 *catch*문 다음의 문장들은 수행이 되지 않는다.

하지만 *try*문 안의 문장들을 수행 중 해당 예외가 발생하면 예외에 해당되는 *catch*문이 수행된다.

숫자를 0으로 나누었을 때 발생하는 예외를 처리하려면 다음과 같이 할 수 있다.

```java
int c;
try {
    c = 4 / 0;
} catch(ArithmeticException e) {
    c = -1;
}
```

`ArithmeticException`이 발생하면 c에 -1을 대입하도록 예외처리한 것이다.

### finally

프로그램 수행 도중 예외가 발생하면 프로그램이 중지되거나 예외처리를 했을 경우 *catch*구문이 실행된다.

하지만 어떤 예외가 발생하더라도 반드시 실행되어야 하는 부분이 있어야 한다면 어떻게 해야 할까?

```java
public class Test {
    public void shouldBeRun() {
        System.out.println("ok thanks.");
    }

    public static void main(String[] args) {
        Test test = new Test();
        int c;
        try {
            c = 4 / 0;
            test.shouldBeRun();
        } catch (ArithmeticException e) {
            c = -1;
        }
    }
}
```

위 예를 보면 `test.shouldBeRun()` 이라는 메소드는 절대로 실행될 수 없을 것이다.

`4/0`에 의해서 `ArithmeticException`이 발생하여 *catch*구문으로 넘어가기 때문이다.

(여기서는 오류가 발생함을 명시적으로 나타내기 위해 4/0 이라는 억지코드를 삽입한 것이다.)

`shouldBeRun()` 메소드는 이름에서 알 수 있듯이 반드시 실행되어야 하는 메소드라고 가정해 보자.

이런 경우를 처리하기 위해 자바는 **finally**라는 구문을 제공한다.

다음과 같은 예를 보도록 하자.

```java
public class Test {
    public void shouldBeRun() {
        System.out.println("ok thanks.");
    }

    public static void main(String[] args) {
        Test test = new Test();
        int c;
        try {
            c = 4 / 0;
        } catch (ArithmeticException e) {
            c = -1;
        } finally {
            test.shouldBeRun();
        }
    }
}
```


**finally** 구문은 *try*문장 수행 중 예외발생 여부에 상관없이 무조건 실행된다.

따라서 위 코드를 실행하면 `test.shouldBeRun()` 메소드가 수행되어 `"ok, thanks"`라는 문장이 출력될 것이다.

***

## 예외 발생시키기(throw, throws)

이번에는 예외를 작성해보고 어떻게 활용할 수 있는가에 대해서 알아보자.

```java
public class Test {
    public void sayNick(String nick) {
        if("fool".equals(nick)) {
            return;
        }
        System.out.println("당신의 별명은 "+nick+" 입니다.");
    }

    public static void main(String[] args) {
        Test test = new Test();
        test.sayNick("fool");
        test.sayNick("genious");
    }
}
```

`sayNick` 메소드는 `fool`이라는 문자열이 입력되면 *return*으로 메소드를 종료하여 별명이 출력되지 못하도록 하고 있다.

### RuntimeException

단순히 별명을 출력하지 못하도록 하는 것이 아니라 적극적으로 예외를 발생시켜 보도록 하자.

다음과 같은 FoolException 클래스를 작성한다.

```java
public class FoolException extends RuntimeException {
}
```

그리고 다음과 같이 예제를 변경시켜본다.

```java
public class Test {
    public void sayNick(String nick) {
        if("fool".equals(nick)) {
            throw new FoolException();
        }
        System.out.println("당신의 별명은 "+nick+" 입니다.");
    }

    public static void main(String[] args) {
        Test test = new Test();
        test.sayNick("fool");
        test.sayNick("genious");
    }
}
```

*return* 부분을 `throw new FoolException()` 이라는 문장으로 변경하였다.

이제 위 프로그램을 실행하면 `"fool"`이라는 입력값으로 `sayNick` 메소드 실행 시 다음과 같은 예외가 발생한다.

```
Exception in thread "main" FoolException
    at Test.sayNick(Test.java:7)
    at Test.main(Test.java:15)
```

*return*으로 메소드를 빠져나오는 대신에 `throw new FoolException()`을 이용해 예외를 강제로 발생시켜 보았다.

`FoolException`이 상속받은 클래스는 `RuntimeException`이다. *Exception*은 크게 두가지로 구분된다.

1. ***RuntimeException***
2. ***Exception***

*RuntimeException*은 실행 시 발생하는 예외이고 *Exception*은 컴파일 시 발생하는 예외이다.

즉, *Exception*은 프로그램 작성 시 이미 예측가능한 예외를 작성할 때 사용하고 *RuntimeException*은 발생 할수도 발생 안 할수도 있는 경우에 작성한다.

다른 말로 *Exception*을 ***Checked Exception***, *RuntimeException*을 ***Unchecked Exception***이라고도 한다.


***

#### 참고 자료

- [점프 투 자바-예외처리](https://wikidocs.net/229)