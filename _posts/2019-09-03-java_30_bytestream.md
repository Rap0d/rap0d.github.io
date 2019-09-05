---
layout: post
title: 30. 바이트 스트림과 파일 입출력
subtitle: 바이트 스트림과 파일 입출력
categories: study
tags: java
---

## Overview

자바에서의 **바이트 스트림**과 **파일 입출력**에 대해서 알아본다.

***

## 바이트 스트림 클래스

**바이트 스트림**은 8비트의 바이트 단위로 바이너리 데이터가 흐르는 스트림이다.

바이트 스트림은 **바이너리 데이터**를 있는 그대로 입출력하기 때문에 **이미지**나 **동영상**의 입출력뿐만 아니라 문자들로 구성된 **텍스트 파일**도 입출력할 수 있다.

다음은 대표적인 바이트 입출력 스트림 클래스이다.

### InputStream/OutputStream

추상 클래스이며, 바이트 입출력 처리를 위한 공통 기능을 가진 슈퍼 클래스이다.

### FileInputStream/FileOutputStream

파일 입출력을 위한 클래스로서, 파일로부터 바이너리 데이터를 읽거나 파일에 바이너리 데이터를 저장할 수 있다.

### DataInputStream/DataOutputStream

이 스트림을 이용하면 *boolean, char, byte, short, int, long, float, double* 타입의 값을 바이너리 형태로 입출력한다.

문자열도 바이너리 형태로 입출력한다.

***

## FileInputStream을 이용한 파일 읽기

바이트 스트림 입력을 위해서 *InputStream* 클래스를 사용할 수 없다.

*InputStream*은 추상 클래스이므로 목적에 따라 *InputStream*을 상속받은 *FileInputStream*이나 *DataInputStream* 클래스를 이용한다.

파일에서 읽을 때는 FileIn*putStream 클래스를 사용한다.

*FileInputStream*의 생성자와 주로 사용되는 메소드는 다음 표와 같다.

| 생성자 | 설명 |
| :---------- | :---------- |
| FileInputStream(File file) | file이 지정하는 파일로부터 입력받는 FileInputStream 생성 |
| FileInputStream(String name) | name이 지정하는 파일로부터 입력받는 FileInputStream 생성 |

| 메소드 | 설명 |
| :---------- | :---------- |
| int read() | 입력 스트림에서 한 바이트를 읽어 int형으로 리턴 |
| int read(byte[] b) | 최대 배열 b의 크기 만큼 바이트를 읽음. 실제 읽은 바이트 수 리턴 |
| int read(byte[] b, int off, int len) | 최대 len개의 바이트를 읽어 b 배열의 off 위치에 저장. 실제 읽은 바이트 수 리턴 |
| int available() | 입력 스트림에서 현재 읽을 수 있는 바이트 수 리턴 |
| void close() | 입력 스트림을 받고 관련된 시스템 자원 해제 |

*FileInputStream* 클래스를 사용하여 파일을 읽는 간단한 예를 들어보자.

### FileInputStream 바이트 스트림 생성

*FileInputStream* 클래스는 파일에 연결한 바이트 스트림을 생성한다.

`C:\test.txt` 파일로부터 바이너리 값을 그대로 읽는 바이트 스트림을 생성하는 코드는 다음과 같다.

```java
FileInputStream fin = new FileInputStream("C:\test.txt");
```

이 코드를 실행하면 `C:\test.txt` 파일을 찾아 열고, 이 파일을 스트림의 입력 단에 연결한 객체 fin을 생성한다.

그리고 나면 위 메소드 표에 보이는 fin 객체의 메소드를 호출하여 파일을 읽을 수 있다.

### 파일 읽기

이제 `fin` 스트림을 이용하여 파일을 읽어보자.

`fin.read()`는 파일 스트림으로부터 한 바이트를 읽어 리턴하며, 파일 전체를 읽어 화면에 출력하는 코드는 다음과 같다.

```java
FileInputStream fin = new FileInputStream("C:\test.txt");
// 파일을 열고 스트림 fin 생성

int c;
while ((c = fin.read()) != -1) {
    // 파일 끝까지 한 바이트씩 c에 읽어들인다.
    System.out.println((char)c);
    // 바이트 c를 문자로 변환하여 화면에 출력한다.
}
```

***파일의 끝(EOF)*** 을 만나면 `fin.read()` 메소드는 -1을 리턴한다.

### 스트림 닫기

더 이상 스트림이 필요없을 때 생성된 스트림을 닫아야 한다.

다음과 같이 `close()` 메소드를 호출하여 스트림을 닫는다.

```java
fin.close();    // 스트림을 닫는다. 더 이상 스트림으로부터 읽을 수 없다.
```

스트림을 닫으면 연결된 파일도 함께 닫힌다.

### 파일을 읽어 화면에 출력하는 사례

*FileInputStream*을 이용하여 사용자 컴퓨터의 파일을 읽고 화면에 출력하는 코드

```java
/**
 * FileInputStreamEx
 */
public class FileInputStreamEx {

    public static void main(String[] args) {
        FileInputStream in = null;
        try {
            in = new FileInputStream("/Users/xxx/Desktop/file_test.txt");
            // 파일과 연결된 바이트 스트림을 생성
            int c;
            while((c = in.read()) != -1) {  // read()는 파일의 끝을 만나면 -1 리턴
                System.out.print((char)c);    // 바이트 c를 문자로 변환하여 출력
            }
            in.close(); // 스트림을 닫는다. 더 이상 스트림으로부터 읽을 수 없다.
        } catch (Exception e) {
            System.out.println("입출력 오류");
        }
    }
}
```

#### 실행 결과

```
File FileInputStream Test

Open .txt file.

SUCCESSED
```

***

## FileOutputStream 클래스를 이용한 파일 쓰기

이제 출력 스트림을 생성하고 사용하는 방법을 설명해보자.

출력 바이트 스트림을 생성하기 위해 *OutputStream*을 직접 사용할 수 없다.

이는 추상 클래스이므로 *FileOutputStream*이나 *DataOutputStream* 클래스를 이용한다.

파일에 쓰고자 하는 경우 *FileOutputStream* 클래스를 이용한다.

*FileOutputStream*의 생성자와 메소드를 다음 표에서 볼 수 있다.

| 생성자 | 설명 |
| :---------- | :---------- |
| FileOutputStream(File file) | file이 지정하는 파일에 출력하는 FileOutputStream 생성 |
| FileOutputStream(String name) | name이 지정하는 파일에 출력하는 FileOutputStream 생성 |
| FileOutputStream(File file, boolean append) | FileOutputStream을 생성하며 append가 true이면 file이 지정하는 파일의 마지막부터 데이터 저장 |
| FileOutputStream(String name, boolean append) | FileOutputStream을 생성하며 append가 true이면 name 이름의 파일의 마지막부터 데이터 저장 |

| 메소드 | 설명 |
| :---------- | :---------- |
| void write(int b) | int 형으로 넘겨진 한 바이트를 출력 스트림으로 출력 |
| void write(byte[] b) | 배열 b의 바이트를 모두 출력 스트림으로 출력 |
| void write(byte[] b, int off, int len) | len 크기만큼 off부터 배열 b를 출력 스트림으로 출력 |
| void flush() | 출력 스트림에서 남아 있는 데이터 모두 출력 |
| void close() | 출력 스트림을 닫고 관련된 시스템 자원 해제 |

***FileOutputStream***을 이용하여 자바 프로그램 내의 변수 값을 그대로 `/Users/xxx/Desktop/file_test.txt`에 기록하는 예를 들어보자.

변수 값을 기록하면 변수에 들어 있는 바이너리값이 그대로 파일에 기록되며 이 파일은 사람이 읽을 수 없는 형태가 된다.

`/Users/xxx/Desktop/file_test.txt` 파일에 바이너리 바이트를 기록하는 출력 스트림의 생성 코드는 다음과 같다.

```java
FileOutputStream fout = new FileOutputStream("/Users/xxx/Desktop/file_test.txt");
```

이 코드는 `/Users/xxx/Desktop/file_test.txt` 파일을 열고, 이 파일에 연결된 스트림 객체 `fout을` 생성한다.

이제 다음과 같이 `fout` 객체의 메소드를 호출하여 파일에 데이터를 기록해보자.

```java
FileOutputStream fout = new FileOutputStream("/Users/xxx/Desktop/file_test.txt");
int num[] = {1, 4, -1, 88, 50};
byte b[] = {7, 51, 3, 4, 1, 24};
for (int i = 0; i < num.length; i++) {
    fout.write(num[i]);
}   // 파일에 num 배열의 정수 값을 바이트 정보로 기록한다.
fout.write(b);  // 파일에 바이트 배열 b의 내용을 모두 그대로 기록한다.
fout.close();
```

실행 결과 `/Users/seungheonchang/Desktop/file_test.txt`은 바이너리 파일이 되며, 다음과 같이 기록된 내용을 가지게 된다.

```

```

`num[]` 배열의 값 `1, 4, -1, 88, 50`의 바이너라 바이트가 파일에 그대로 기록된 것을 볼 수 있다.