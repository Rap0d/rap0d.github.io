---
layout: post
title: Spring Version 3에서 4로 Migration
subtitle: Spring Version 3에서 4로 Migration
categories: study
tags: web
---

![Spring](/assets/img/logo/spring-logo.png)

## Overview

STS에서 Spring MVC Project를 생성하면 기본 Spring Framework 버전은 3.1.1이다.

이것을 4 버전으로 변경하는 과정을 알아본다.

***

## Environment

### 기본 환경

> Spring Framework : 3.1.1  
> java version : 1.6  
> servlet-api : 2.5  
> jsp-api : 2.1  
> Maven compiler source 1.6  
> Maven compiler target 1.6  

### 변경 될 환경

> Spring Framework : 4.3.26  
> java version : 1.8  
> servlet-api : 3.0  
> jsp-api : 2.2  
> Maven compiler source 1.8  
> Maven compiler target 1.8  

[Spring Version List](https://spring.io/projects/spring-framework#learn)에서 Spring Framework의 Release 버전을 볼 수 있다.

***

## 설정 변경

`pom.xml`에서 다음 과정을 통해 버전을 수정한다.

1) *java version*을 `1.6`에서 `1.8`로, *springframework version*을 `3.1.1`에서 `4.3.26`으로 변경  

```xml
<properties>
    <java-version>1.8</java-version>
    <org.springframework-version>4.3.4.RELEASE<org.springframework-version>
    <org.aspectj-version>1.6.10</org.aspectj-version>
    <org.slf4j-version>1.6.6</org.slf4j-version>
</properties>
```

2-1) *Servlet api* 버전을 `2.5`에서 `3.0.1` 로 변경하고, *artifactId* 도 `servlet-api` 에서 `javax.servlet-api`로 변경

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.0.1</version>
    <scope>provided</scope>
</dependency>
```

2-2) *jsp api* 버전을 `2.1`에서 `2.2`로 변경

```xml
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2</version>
    <scope>provided</scope>
</dependency>
```

2-3) *maven compiler*의 *source*와 *target*을 `1.8`로 변경

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.5.1</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <compilerArgument>-Xlint:all</compilerArgument>
        <showWarnings>true</showWarnings>
        <showDeprecation>true</showDeprecation>
    </configuration>
</plugin>
```

3) *Servlet Spec*이 변경되면 `web.xml`의 DTD도 버전에 맞게 수정되어야 함

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
</web-app>
```
`xsi:schemaLocation`과 `<web-app version>`을 3.0으로 수정한다.


4) *Project Properties* 수정

1. Java Build Path의 Libraries에서 JRE System Library 수정
2. Java Compiler의 Compiler level을 `1.8`로 지정
3. Project Facets에서 Dynamic Web Module을 `3.0`, java는 `1.8`로 지정
4. Project의 팝업 메뉴에서 Maven -> Update Project를 선택하여 설정 내용 적용

***

## 참고 자료

- [https://offbyone.tistory.com/16](https://offbyone.tistory.com/16)