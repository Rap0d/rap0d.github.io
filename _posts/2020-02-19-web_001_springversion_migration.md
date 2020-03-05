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
> Maven compiler
> - source 1.6  
> - target 1.6  

### 변경 될 환경

> Spring Framework : 4.3.26  
> java version : 1.8  
> servlet-api : 3.0  
> jsp-api : 2.2  
> Maven compiler
> - source 1.8  
> - target 1.8  

[Spring Version List](https://spring.io/projects/spring-framework#learn)에서 Spring Framework의 Release 버전을 볼 수 있다.

***

## 설정 변경

pom.xml에서 다음 과정을 통해 버전을 수정한다.

1) java version을 1.6에서 1.8로, springframework version을 3.1.1에서 4.3.26으로 변경

    ```xml
    <properties>
        <java-version>1.8</java-version>
        <org.springframework-version>4.3.4.RELEASE<org.springframework-version>
        <org.aspectj-version>1.6.10</org.aspectj-version>
        <org.slf4j-version>1.6.6</org.slf4j-version>
    </properties>
    ```

2) Servlet api 버전을 2.5에서 3.0.1 로 변경하고, artifactId 도 servlet-api 에서 javax.servlet-api로 변경

    ```xml
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.0.1</version>
        <scope>provided</scope>
    </dependency>
    ```