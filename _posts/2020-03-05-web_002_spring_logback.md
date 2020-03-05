---
layout: post
title: Spring에서 log4j를 loackback으로 변경하기
subtitle: Spring에서 log4j를 loackback으로 변경하기
categories: study
tags: web
---

![Spring](/assets/img/logo/spring-logo.png)

## Overview

`Logging`은 오류 확인 및 처리를 위한 용도로 사용된다.

리소스를 많이 사용하여 성능에 영향을 줄 수 있는 `System.out.println()`대신에 `Logging`을 사용하면 에러 및 장애가 발생했을 때 확인할 수 있는 최소한의 정보(날씨, 시간, 로그 타입 등)를 제공받을 수 있으므로 `System.out.println()` 대신 `Logging`을 사용하는 습관을 가지는게 좋다.

## 사용 방법

- `pom.xml`의 dependency 수정
  - 프로젝트 생성 시 추가되는 Exclude Commons Logging in favor of SLF4j, Logging과 관련된 라이브러리 삭제
  ```xml
    <org.slf4j-version>1.6.6</org.slf4j-version>
    <!--  Exclude Commons Logging in favor of SLF4j -->
    <exclusion>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
    </exclusion>
    
    <!-- Logging-->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>${org.slf4j-version}</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>${org.slf4j-version}</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>${org.slf4j-version}</version>
        <scope>runtime</scope>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.15</version>
        <exclusions>
            <exclusion>
                <groupId>javax.mail</groupId>
                <artifactId>mail</artifactId>
            </exclusion>
            <exclusion>
                <groupId>javax.jms</groupId>
                <artifactId>jms</artifactId>
            </exclusion>
            <exclusion>
                <groupId>com.sun.jdmk</groupId>
                <artifactId>jmxtools</artifactId>
            </exclusion>
            <exclusion>
                <groupId>com.sun.jmx</groupId>
                <artifactId>jmxri</artifactId>
            </exclusion>
        </exclusions>
        <scope>runtime</scope>
    </dependency>
  ```
  - logback과 관련된 라이브러리 추가
  ```xml
    <slf4j.version>1.7.21</slf4j.version>
    <logback.version>1.1.7</logback.version>
    
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>${slf4j.version}</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>${logback.version}</version>
    </dependency>
    ```
- `logback.xml` 추가
  - `src/main/resources`, `src/test/resources` 위치의 log4j.xml 파일을 삭제하고 logback-spring.xml 파일 추가
  ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>  
        <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
            <layout class="ch.qos.logback.classic.PatternLayout">
                <pattern>%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n</pattern>
            </layout>
        </appender>

        <logger name="org.springframework" level="info" additivity="false">
            <appender-ref ref="STDOUT"/>
        </logger>

        <logger name="com.spring.board" level="debug" additivity="false">
            <appender-ref ref="STDOUT"/>
        </logger>
        
        <root level="error">
            <appender-ref ref="STDOUT"/>
        </root>
    </configuration>
  ```

***

## 사용

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
protected final Logger logger = LoggerFactory.getLogger(this.getClass());
```

`System.out.println()` 자리에 `logger.info()`를 사용하여 콘솔에서 볼 수 있다.

***

## 참고 자료

- [https://offbyone.tistory.com/16](https://offbyone.tistory.com/16)