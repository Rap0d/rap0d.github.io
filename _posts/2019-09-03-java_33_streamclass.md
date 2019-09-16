---
layout: post
title: 33. 문자 스트림 클래스
subtitle: 문자 스트림 클래스
categories: study
tags: java
---

## Overview

문자 스트림은 유니코드로 된 문자 단위의 데이터를 다루는 스트림이다.

문자화되지 않는 바이너리 값들은 이 스트림에 존재할 수 없으며, 따라서 이미지 등과 같은 바이너리 정보는 처리할 수 없다.

***

## 문자 스트림 클래스

문자 스트림을 다루는 클래스들은 데이터를 로컬 문자 집합 내의 문자로 자동 변환한다.

이들 스트림 클래스는 한글과 같이 문자로 인식되는 데이터, 즉 텍스트 혹은 텍스트 파일만 처리할 수 있다.

문자 스트림을 다루는 모든 클래스는 이름에 Reader, Writer를 포함하고 있으며, 주요 클래스는 다음과 같다.

### Reader / Writer

추상 클래스이며, 문자 입출력 처리를 위한 공통 기능을 가진 슈퍼 클래스이다.

### InputStreamReader / OutputStreamWriter
이 두 클래스는 바이트 스트림과 문자 스트림을 연결시켜주는 다리(bridge) 역할을 한다.

이 두 클래스는 지정된 문자 집합을 이용하는데, InputStreamReader는 바이트들을 읽어 지정된 문자 집합 내의 문자로 인코딩하며, OutputStreamWriter는 문자를 바이트들로 디코딩하여 스트림으로 출력한다.

### FileReader / FileWriter

FileReader나 FileWriter는 텍스트 파일로부터 직접 문자 데이터를 읽고 기록할 수 있다.

또한 FileReader는 FileInputStream과 연결하여 읽어 들인 바이트를 문자 데이터로 변환하기도 하며, FileWriter는 FileOutputStream을 이용하여 문자 스트림을 바이트 스트림으로 변환하여 파일에 기록하기도 한다.

***

## FileReader를 이용한 텍스트 파일 읽기

다음 표는 FileReader 클래스와 InputStreamReader 생성자를 보여준다.

| 생성자 | 설명 |
| :---------- | :---------- |
| InputStreamReader(InputStream in | in으로부터 읽는 기본 문자 집합의 InputStreamReader 생성 |
| InputStreamReader(InputStream in, Charset cs | in으로부터 읽는 cs 문자 집합의 InputStreamReader 생성 |
| InputStreamReader(InputStream in, String charsetName | in으로부터 읽는 charsetName 문자 집합의 InputStreamReader 생성|
| FileReader(File file) | file로부터 읽는 FileReader 생성 |
| FileReader(String name) | name 이름의 파일로부터 읽는 FileReader 생성|

| 메소드 | 설명 |
| :---------- | :---------- |
| int read() | 한 개의 문자를 읽어 정수형으로 리턴 |
| int read(char[] cbuf) | 문자들을 읽어 cbuf 배열에 저장하고 읽은 개수 리턴 |
| int read(char[] cbuf, int off, int len) | 최대 len 크기만큼 읽어 off부터 저장하고 실제 읽은 개수 리턴 |
| boolean ready() | 입력 스트림이 문자를 읽어들일 수 있는 상태인지 리턴 |
| String getEncoding | 스트림이 사용하는 문자 집합의 이름 리턴 |
| void reset() | 스트림 리셋, 스트림이 마킹되어 있으면 그 위치부터 시작 |
| void close() | 입력 스트림을 닫고 관련된 시스템 자원 해제 |

FileReader의 생성자는 File 객체나 문자열이 가리키는 파일과 연결된 문자 스트림을 생성한다.

그러나 InputStreamReader의 생성자에 InputStream 객체가 전달되며, InputStreamReader는 InputStream 객체가 파일에서 읽은 바이트 데이터를 문자 집합을 통해 문자로 변환한다.

이를 위해 InputStreamReader의 생성자에는 문자 집합을 지정하는 Charset 객체를 전달하도록 하고 있다.

다음 예제는 InputStreamReader를 통해 파일을 읽는 예를 보인다.

```java
public class FileReadHangulSuccess {
    public static void main(String[] args) {
        InputStreamReader in = null;
        FileInputStream fin = null;
        try {
            fin = new FileInputStream("c:\\tmp\\hangul.txt");
            in = new InputStreamReader(fin, "MS949");   // 올바른 문자 집합 지정
            int c;
            System.out.println("인코딩 문자 집합은 " + in.getEncoding());
            while ((c = in.read()) != -1) {
                System.out.println((char)c);
            }
            in.close();
            fin.close();
        } catch (IOException e) {
            System.out.println("입출력 오류");
        }
    }
}
```

***

## FileReader 문자 스트림 생성

`c:\test.txt` 파일과 연결된 문자 입력 스트림을 생성하는 코드는 다음과 같다.

```java
FileReader fin = new FileReader("c:\\test.txt");
```

이 코드를 실행하면 `c:\test.txt` 파일을 찾아 열고, 이 파일에서 문자 단위로 읽을 수 있는 스트림 객체 fin을 생성한다.

위의 메소드를 이용하여 파일의 모든 문자를 읽어 화면에 출력하는 예는 다음과 같다.

```java

public class FileReaderEx {
    public static void main(String[] args) {
        FileReader in = null;
        try {
            in = new FileReader("c:\\windows\\system.ini"); // 파일로부터 문자 입력 스트림 생성

            int c;
            while ((c = in.read()) != -1) {
                System.out.println((char)c);
            }
            in.close();
        } catch (IOException e) {
            System.out.println("입출력 오류");
        }
    }
}
```

***

## 문자 집합과 InputStreamReader를 이용한 텍스트 파일 읽기

InputStreamReader 클래스는 InputStream 클래스 객체를 이용하도록 되어 있다.

그리고 InputStream이 읽어 들인 바이트들이 어떤 문자 집합에 속한 문자인지 구분하기 위해 문자 집합을 지정하기도 한다.

만일 지정된 문자 집합에 속하지 않으면 해독할 수 없는 글자가 된다.

### InputStreamReader 문자 스트림 생성

운영체제에서 한글 문서를 작성했을때, 이 텍스트 파일읅 읽기 위해 InputStreamReader 스트림을 생성하는 코드는 다음과 같다.

```java
FileInputStream fin = new FileInputStream("c:\\tmp\\hangul.txt");
InputStreamReader in = new InputStreamReader(fin, "UTF-8");
```

***

## FileWriter를 이용한 텍스트 파일 쓰기

다음 표는 출력 문자 스트림 클래스의 생성자와 메소드들을 보여준다.

| 생성자 | 설명 |
| :---------- | :---------- |
| OutputStreamWriter(OutputStream out | out에 출력하는 기본 문자 집합의 OutputStreamWriter 생성 |
| OutputStreamWriter(OutputStream out, Charset cs | out에 출력하는 cs 문자 집합의 OutputStreamWriter 생성 |
| OutputStreamWriter(OutputStream out, String charsetName | charsetName 문자 집합의 OutputStreamWriter 생성|
| FileWriter(File file) | file에 데이터를 저장할 FileWriter 생성 |
| FileWriter(String name) | name 파일에 데이터를 저장할 FileWriter 생성 |
| FileWriter(File file, boolean append) | FileWriter를 생성하며, append가 true이면 파일의 마지막부터 데이터를 저장 |
| FileWriter(String name, boolean append) | FileWriter를 생성하며, append가 true이면 파일의 마지막부터 데이터를 저장 |

| 메소드 | 설명 |
| :---------- | :---------- |
| void write(int c) | c를 char로 변환하여 한 개의 문자 출력 |
| void write(String str, int off, int len) | 인덱스 off부터 len개의 문자를 str 문자열에서 출력 |
| void write(char[] cbuf, int off, int len) | 인덱스 off부터 len개의 문자를 cbuf 배열에서 출력 |
| void flush() | 스트림에 남아있는 데이터 모두 출력 |
| String getEncoding | 스트림이 사용하는 문자 집합의 이름 리턴 |
| void close() | 출력 스트림을 닫고 관련된 시스템 자원 해제 |

InputStreamReader와 유사하게 OutputStreamWriter 생성자도 OutputStream과 문자 집합을 인자로 받는다.

FileWriter의 생성자는 File 객체나 문자열이 가리키는 파일과 연결된 문자 스트림을 생성한다.

이제 파일 출력 스트림을 만들고 파일에 텍스트를 쓰는 예를 들어보자.

### FileWriter 문자 출력 스트림 생성

`c:\test.txt` 파일에 텍스트를 쓰는 출력 스트림 생성 코드는 다음과 같다.

```java
FileWriter fout = new FileWriter("c:\\test.txt");
```

이 코드를 실행하면 `c:\test.txt` 파일을 찾아 열고, 파일이 없는 경우 빈 파일을 생성한 후, 문자 단위로 쓰는 스트림 객체 fout을 생성한다.

파일에 문자를 기록하는 코드는 다음과 같다.

```java
FileWriter fout = new FileWriter("c:\\test.txt");
fout.write('A');    // 문자 'A'를 파일에 기록
fout.close();       // 스트림을 닫는다. 파일을 저장하고 닫는다.
```

### 키보드 입력을 파일로 저장하기

키보드에서 입력받는 데이터를 `c:\test.txt` 파일에 저장하는 프로그램

```java
public class FileWriterEx {
    public static void main(String[] args) {
        InputStreamReader in = new InputStreamReader(System.in);
        // 콘솔과 연결된 입력 문자 스트림 생성

        FileWriter fout = null;
        int c;
        try {
            fout = new FileWriter("c:\\test.txt");
            // 파일과 연결된 출력 문자 스트림 생성
            while((c = in.read()) != -1) {
                fout.write(c);
            }
        } catch (IOException e) {
            System.out.println("입출력 오류");
        }
    }
}
```

