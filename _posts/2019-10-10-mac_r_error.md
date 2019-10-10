---
layout: post
title: macOS환경의 R에서 rJava가 load되지 않는 문제 해결
subtitle: macOS환경의 R에서 rJava가 load되지 않는 문제 해결
categories: tip
tags: mactip
---

![r](/assets/img/logo/r-logo.png)

## Overview

macOS환경의 R에서 rJava가 load되지 않는 문제 해결

***

## 사용된 환경

> OS : macOS Catalina 10.15(19A583)  
> R : 3.6.1  
> R Studio : 1.2.5001  
> JDK : 11.0.1  
> 기준 일자 : 2019-10-10  

***

## 이슈

R에서 rJava를 설치하고 로드 하는 과정에서 다음과 같은 에러가 발생

```
Error: package or namespace load failed for ‘rJava’:
 .onLoad failed in loadNamespace() for 'rJava', details:
  call: dyn.load(file, DLLpath = DLLpath, ...)
  error: unable to load shared object '/Library/Frameworks/R.framework/Versions/3.6/Resources/library/rJava/libs/rJava.so':
  dlopen(/Library/Frameworks/R.framework/Versions/3.6/Resources/library/rJava/libs/rJava.so, 6): Library not loaded: /Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home/lib/server/libjvm.dylib
  Referenced from: /Library/Frameworks/R.framework/Versions/3.6/Resources/library/rJava/libs/rJava.so
  Reason: image not found
```

***

## 해결

우선 [MAC OS :: tm 및 KoNLP 로딩오류 문제](https://rstudio-pubs-static.s3.amazonaws.com/390520_0e53f55571474119b82a059e9dc1403d.html)의 문서를 따라 해결 한다.

위의 문서에서 해결이 안되었을 경우 위의 에러 메시지에서 요구하는 *JDK 11.0.1*을 설치하여 PATH에 등록한 후 사용한다.

*JDK 11.0.1*은 ***[이곳](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase11-5116896.html)***에서 다운받을 수 있다.

해결 방법은 다음과 같다.

1. R Studio 콘솔에서 다음 코드를 입력한다.
    ```R
    Sys.setenv("JAVA_HOME" = '/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home')
    dyn.load('/Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home/lib/server/libjvm.dylib')
    ```
2. `Sys.getenv()` 명령어를 통해 JAVA_HOME 경로가 잘 들어갔는지 확인한다.
3. *Terminal*에서 다음 명령어를 입력한다.
    ```
    sudo R CMD javareconf
    ```
4. 콘솔창에 `Yes, No` 선택하는 문장이 나오면 `Yes`를 선택하고 넘어간다.
5. `library(rJava)`를 입력하고 `.jinit()`을 입력하여 정상적으로 로드되는지 확인한다.

***

## 참고 문서

- [R에서 rJava 에러날 때](https://puture.tistory.com/393)
- [MAC OS :: tm 및 KoNLP 로딩오류 문제](https://rstudio-pubs-static.s3.amazonaws.com/390520_0e53f55571474119b82a059e9dc1403d.html)
- [Oracle JDK 11.0.1](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase11-5116896.html)