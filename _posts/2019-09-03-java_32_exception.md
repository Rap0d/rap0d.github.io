---
layout: post
title: 32. 예외처리
subtitle: 예외처리
categories: study
tags: java
---

![javalogo](/assets/img/logo/java-logo.png)

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

4를 0으로 나누려니까 `ArithmeticException`라는 이름의 예외가 발생한다.

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

## 예외처리하기

유연한 프로그래밍을 위한 예외처리의 기법에 대해 살펴보자.

다음은 예외처리를 위한 **try, catch**문의 기본 구조이다.

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
        if ("fool".equals(nick)) {
            return;
        }
        System.out.println("당신의 별명은 " + nick + " 입니다.");
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
        if ("fool".equals(nick)) {
            throw new FoolException();
        }
        System.out.println("당신의 별명은 " + nick + " 입니다.");
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

### Exception

그렇다면 `FoolException`을 다음과 같이 변경해보자.

```java
public class FoolException extends Exception {
}
```

`RuntimeException`을 상속하던 것을 `Exception`을 상속하도록 변경했다.

이렇게 하면 `Test` 클래스에서 컴파일 오류가 발생할 것이다.

예측 가능한 *Checked Exception*이기 때문에 예외처리를 컴파일러가 강제하기 때문이다.

다음과 같이 변경해야 정상적으로 컴파일이 될 것이다.

```java
public class Test {
    public void sayNick(String nick) {
        try {
            if ("fool".equals(nick)) {
                throw new FoolException();
            }
            System.out.println("당신의 별명은 " + nick + " 입니다.");
        } catch(FoolException e) {
            System.err.println("FoolException이 발생했습니다.");
        }
    }

    public static void main(String[] args) {
        Test test = new Test();
        test.sayNick("fool");
        test.sayNick("genious");
    }
}
```

`sayNick` 메소드에서 *try... catch* 문으로 `FoolException`을 처리했다.

### 예외 던지기(throws)

위 예제를 보면 `sayNick`이라는 메소드에서 `FoolException`을 발생시키고 예외처리도 `sayNick`이라는 메소드에서 했다.

`sayNick`을 호출한 곳에서 `FoolException`을 처리하도록 예외를 위로 던질 수 있는 방법이 있다.

다음 예제를 보자.

```java
public void sayNick(String nick) throws FoolException {
    if ("fool".equals(nick)) {
        throw new FoolException();
    }
    System.out.println("당신의 별명은 " + nick + " 입니다.");
}
```

`sayNick` 메소드 뒷부분에 *throws*라는 구문을 이용하여 `FoolException`을 위로 보낼 수가 있다. ("예외를 뒤로 미루기"라고도 한다.)

위와 같이 `sayNick` 메소드를 변경하면 `main` 메소드에서 컴파일 에러가 발생할 것이다.

*throws* 구문 때문에 `FoolException`의 예외를 처리해야 하는 대상이 `sayNick` 메소드에서 `main` 메소드(`sayNick` 메소드를 호출하는 메소드)로 변경되었기 때문이다.

`main` 메소드는 컴파일이 되기 위해서 다음과 같이 변경되어야 한다.

```java
public static void main(String[] args) {
    Test test = new Test();
    try {
        test.sayNick("fool");
        test.sayNick("genious");
    } catch(FoolException e) {
        System.err.println("FoolException이 발생했습니다.");
    }
}
```

`main` 메소드에서 *try... catch*로 `sayNick` 메소드에 대한 `FoolException` 예외를 처리하였다.

자, 이제 한가지 고민이 남아있다. 

`FoolException` 처리를 `sayNick` 메소드에서 하는것이 좋을까? 아니면 `main` 메소드에서 하는것이 좋을까?

`sayNick` 메소드에서 처리하는 것과 `main` 메소드에서 처리하는 것에는 아주 큰 차이가 있다.

`sayNick` 메소드에서 예외를 처리하는 경우에는 다음의 두 문장이 모두 수행이된다.

```java
test.sayNick("fool");
test.sayNick("genious");
```

물론 `test.sayNick("fool");` 문장 수행 시에는 `FoolException`이 발생하겠지만 그 다음 문장인 `test.sayNick("genious");` 역시 수행이 된다.

하지만 `main` 메소드에서 다음과 같이 예외처리를 한 경우에는 두 번째 문장인 `test.sayNick("genious");`가 수행되지 않을 것이다.

이미 첫 번째 문장에서 예외가 발생하여 *catch* 문으로 빠져버리기 때문이다.

```java
try {
    test.sayNick("fool");
    test.sayNick("genious");
} catch(FoolException e) {
    System.err.println("FoolException이 발생했습니다.");
}
```

프로그래밍 시 *Exception*을 처리하는 위치는 대단히 중요하다.

프로그램의 수행여부를 결정하기도 하고 트랜잭션 처리와도 밀접한 관계가 있기 때문이다.

***

## 트랜잭션 (Transaction)

**트랜잭션**과 **예외처리**는 매우 밀접한 관련이 있다.

트랜잭션과 예외처리가 서로 어떤 관련이 있는지 알아보도록 하자.

트랜잭션은 하나의 작업 단위를 뜻한다.

쇼핑몰의 *상품발송*이라는 트랜잭션을 가정 해 보자.

*상품발송*이라는 트랜잭션에는 다음과 같은 작업들이 있을 수 있다.

- 포장
- 영수증발행
- 발송

이 3가지 일들 중 하나라도 실패하면 3가지 모두 취소하고 *상품발송* 전 상태로 되돌리고 싶을 것이다. (모두 취소하지 않으면 데이터의 **정합성**이 크게 흔들리게 된다. 이렇게 모두 취소하는 행위를 **Rollback**이라고 한다.)

프로그램이 다음과 같이 작성되어 있다고 가정해보자. (※ 아래는 실제 코드가 아니라 어떻게 동작하는지를 간략하게 표현한 *pseudo* 코드이다.)

```
상품발송() {
    포장();
    영수증발행();
    발송();
}

포장() {
   ...
}

영수증발행() {
   ...
}

발송() {
   ...
}
```

쇼핑몰 운영자는 **포장, 영수증발행, 발송**이라는 메소드 중 1가지라도 실패하면 모두 취소하고 싶어한다.

이런 경우 어떻게 예외처리를 하는 것이 좋겠는가?

다음과 같이 **포장, 영수증발행, 발송** 메소드에서는 예외를 *throw*하고, 상품발송 메소드에서 *throw*된 예외를 처리하여 모두 취소하는 것이 완벽한 트랜잭션 처리 방법이다.

```
상품발송() {
    try {
        포장();
        영수증발행();
        발송();
    } catch(예외) {
       모두취소();
    }
}

포장() throws 예외 {
   ...
}

영수증발행() throws 예외 {
   ...
}

발송() throws 예외 {
   ...
}
```

위와 같이 코드를 작성하면 **포장, 영수증발행, 발송**이라는 세 개의 단위작업 중 하나라도 실패할 경우 ***예외***가 발생되어 상품발송이 모두 취소 될 것이다.

만약 위 처럼 *상품발송* 메소드가 아닌 포장, 영수증발행, 발송 메소드에 각각 예외처리가 되어 있다고 가정 해 보자.

```
상품발송() {
    포장();
    영수증발행();
    발송();
}

포장(){
    try {
       ...
    } catch(예외) {
       포장취소();
    }
}

영수증발행() {
    try {
       ...
    } catch(예외) {
       영수증발행취소();
    }
}

발송() {
    try {
       ...
    } catch(예외) {
       발송취소();
    }
}
```

이렇게 각각의 메소드에 예외가 처리되어 있다면 포장은 되었는데 발송은 안되고 포장도 안되었는데 발송이 되고 이런 뒤죽박죽의 상황이 연출될 것이다.

실제 프로젝트에서도 두번째 경우처럼 트랜잭션관리를 잘못하는 경우는 일종의 재앙에 가깝다.

이 포스트에서 자바의 예외처리에 대해서 알아보았다.

사실 예외처리는 자바에서 난이도가 있는 부분에 속한다.

보통 프로그래머의 실력을 평가 할 때 이 예외처리를 어떻게 하고 있는지를 보면 그 사람의 실력을 어느정도 가늠해 볼 수 있다고들 말한다.

예외처리는 부분만 알아서는 안되고 전체를 관통하여 모두 알아야만 정확히 할 수 있기 때문이다.

***

### 출처

- [점프 투 자바-예외처리](https://wikidocs.net/229)