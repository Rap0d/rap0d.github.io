---
layout: post
title: 34. 버퍼 입출력
subtitle: 버퍼 입출력
categories: study
tags: java
---

![javalogo](/assets/img/logo/java-logo.png)

# Overview 

입출력 스트림은 운영체제 API를 호출하여 입출력 장치와 프로그램 사이에서 데이터가 전송되도록 한다.

파일 출력 스트림 객체는 파일 쓰기를 실행하는 메소드가 실행될 때마다 운영체제 API를 호출하여 파일에 쓰도록 시킨다.

운영체제 API는 저장 매체에게 명령을 내리고 파일에 데이터를 기록한다.

자주 운영체제 API가 호출될 수록 저장매체나 네트워크 장치가 자주 작동하게 되어 시스템의 효율은 나빠진다.

만일 스트림이 버퍼(buffer)를 가지게 되면 보다 효율적으로 작동할 수 있다.

버퍼란 데이터를 일시적으로 저장하기 위한 메모리이다.

파일 출력 스트림이 쓰기가 이루어진 데이터를 저장 매체에 기록하지 않고 버퍼에 모아두었다가, 버퍼가 꽉 차게 될 때 비로소 한번에 운영체제 API를 호출하여 파일에 쓰게 하면, 운영체제의 부담을 줄이고 장치를 구동하는 일이 줄어들게 되어 시스템의 속도나 효율이 올라가게 될 것이다.

앞의 포스트에서 다룬 입출력 스트림은 버퍼(buffer) 메모리를 가지지 않는 unbuffered I/O 방식이었다.

버퍼 입출력(Buffered I/O)이란 다음 그림과 같이 입출력 스트림이 버퍼를 가지고 보다 효율적으로 입출력을 처리하는 방식이다.

![버퍼 스트림](/assets/img/study/java/190916_fig_01.png "버퍼 스트림")

버퍼를 가지는 스트림을 버퍼 스트림(Buffered Stream)이라고 부른다.

위 그림에서 입력 버퍼 스트림은 입력 장치로부터 입력된 데이터를 버퍼에 저장하였다가 프로그램으로 보내며, 프로그램에서 출력된 데이터 역시 출력 버퍼 스트림의 버퍼에 일시적으로 저장되었다가 출력 장치에 출력된다.

버퍼 스트림 역시 스트림이 다루는 데이터의 타입에 따라 바이트 버퍼 스트림과 문자 버퍼 스트림으로 구분된다.

바이트 버퍼 스트림을 구현한 입출력 클래스는 BufferedInputStream과 BufferedOutputStream이 있으며, 문자 버퍼 스트림을 구현한 클래스는 BufferedReader와 BufferedWriter가 있다.

***

# 버퍼 입출력 스트림 생성 및 활용

버퍼 입출력 스트림은 내부에 버퍼를 가진다는 사실만 다를 뿐이지 개발자에게 보이는 형식적인 면은 입출력 스트림과 거의 동일하다.

다음 표는 바이트 버퍼 스트림을 구현한 BufferedInputStream과 BufferedOutputStream, 문자 버퍼 스트림을 구현한 BufferedReader와 BufferedWriter의 생성자를 각각 보여준다.

| 생성자 | 설명 |
| :---------- | :---------- |
| BufferedInputStream(InputStream in) | in을 연결하는 디폴트 크기의 입력 버퍼 스트림 객체 생성 |
| BufferedInputStream(InputStream in, int size) | in을 연결하는 size 크기의 입력 버퍼 스트림 객체 생성 |
| BufferedOutputStream(OutputStream out) | out을 연결하는 디폴트 크기의 출력 버퍼 스트림 생성 |
| BufferedOutputStream(OutputStream out, int size) | out을 연결하는 디폴트 크기의 출력 버퍼 스트림 생성 |

| 생성자 | 설명 |
| :---------- | :---------- |
| BufferedReader(Reader in) | in을 연결하는 디폴트 크기의 문자 입력 버퍼 스트림 생성 |
| BufferedReader(Reader in, int size) | in을 연결하는 size 크기의 문자 입력 버퍼 스트림 생성 |
| BufferedWriter(Writer out) | out을 연결하는 디폴트 크기의 문자 출력 버퍼 스트림 생성 |
| BufferedWriter(Writer out, int size) | out을 연결하는 size 크기의 문자 출력 버퍼 스트림 생성 |

위 두 표에서 볼 수 있듯이 버퍼 스트림 클래스의 생성자는 모두 ***바이트 스트림 또는 문자 스트림과 연결하여 사용***하고, 버퍼의 크기를 생성자에 지정한다.

BufferedInputStream과 BufferedOutputStream의 메소드는 InputStream, OutputStream 클래스와 각각 같으며, BufferedReader와 BufferedWriter의 메소드는 Reader, Writer 클래스와 각각 같다.

화면에 출력하는 버퍼 출력 스트림을 BufferedOutputStream 클래스를 이용하여 생성해보자.

다음과 같이 출력 버퍼 스트림을 생성한다.

```java
BufferedOutputStream out = new BufferedOutputStream(System.out, 20);
// 20바이트 버퍼
```

위 라인은 20바이트 크기의 버퍼를 가지고, System.out 표준 스트림을 이용하여 화면에 출력하는 버퍼 스트림을 생성한다.

이제, 사용자의 컴퓨터의 파일을 읽어 화면에 출력하는 프로그램을 작성하면 다음과 같다.

```java
BufferedOutputStream bos = new BufferedOutputStream(System.out, 20);
FileReader fin = new FileReader("File path");

int c;
while((c = fin.read()) != -1) {
    bos.write((char)c);
    // 읽은 문자를 버퍼 출력 스트림에 쓴다.
}
fin.close();
bos.close();
```

## 버퍼에 남아있는 데이터 출력

버퍼 스트림은 버퍼를 가지고 있기 때문에 버퍼가 꽉 찼을때만 출력되는 특징을 가진다.

그러므로 프로그램에서 실제 데이터를 출력했지만 출력 장치에는 아직 출력되지 않을 수도 있다.

버퍼가 다 채워지지 않는 상태에서 버퍼에 있는 데이터를 모두 출력시키고자 하면 flush() 메소드를 이용하면 된다.

버퍼 출력 스트림을 다루는 클래스는 공통적으로 flush() 메소드를 가지고 있다.

다음은 flush()를 호출하여 버퍼에 있는 데이터를 강제로 화면에 출력하는 코드이다.

```java
BufferedOutputStream bos = new BufferedOutputStream(Systyem.out, 20);
...
bos.flush();    // bos 스트림의 버퍼에 있는 데이터를 모두 장치에 출력
```

flush()가 호출되지 않으면 bos의 버퍼가 꽉 찰 때까지 출력한 데이터가 화면에 나타나지 않을 수 있다.

## 버퍼 스트림을 이용하는 출력

버퍼 크기를 5로 하고, 표준 출력 스트림과 연결된 버퍼 출력 스트림을 생성한다.

그리고 키보드에 입력받은 문자를 출력 스트림에 출력하고, 키보드로부터 입력의 끝을 나타내는 키가 입력되면, 버퍼에 남아있는 모든 문자를 강제로 출력하는 프로그램이다.

```java
/**
 * BufferedIOEx
 */
public class BufferedIOEx {
    public static void main(String[] args) {
        InputStreamReader in = new InputStreamReader(System.in);
        // 콘솔과 연결된 입력 문자 스트림 생성
        BufferedOutputStream out = new BufferedOutputStream(System.out, 5);

        try {
            int c;
            while((c = in.read()) != -1) {
                // ctrl-z가 입력될 때까지 반복
                out.write(c);
                // 실제 버퍼가 다 찰 때만 문자가 화면으로 출력
            }
            out.flush();
            in.close();
            out.close();
        } catch (Exception e) {
            System.out.println("IO Exception");
        }
    }
}
```