---
layout: post
title: macOS에서 Mariadb Stop이 안될때 해결법
subtitle: macOS에서 Mariadb Stop이 안될때 해결법
categories: tip
tags: mactip
---

![zsh-logo](/assets/img/logo/mariadb-logo.png)

## Overview

macOS에서 Mariadb가 꺼지지 않는 현상을 해결하는 과정을 담은 포스트

***

## 사용된 환경

> OS : macOS Catalina 10.15.4(19E287)
> zsh : zsh 5.7.1 (x86_64-apple-darwin19.0)  
> 기준 일자 : 2020-05-09  

***

## ISSUE

mariadb를 macOS환경에서 [brew로 설치](/tip/2020/05/09/mac_mariadb_install/)했다고 가정한다.

mariadb를 일반적인 방법으로 시작하고 끄는 방법은 다음과 같다.

```
# Start
mysql.server start

# Stop
mysql.server stop
```

이 때, Start는 잘 작동되어 DB에 접속할 수 있지만, Stop 명령어를 사용하면 프로세스가 완료되지 않는 현상이 일어난다.

***

## 해결

1. 우선 Terminal에서 mariadb 프로세스를 완전히 끈다.

```
# mysqld 종료
pkill mysqld
killall mysqld
# mysqld_safe 종료
pkill mysqld_safe          
```

2. brew에서 mariadb가 동작하고 있는지 확인한다.

```
brew services list

# Name    Status  User           Plist
# mariadb stopped
```

종료된 것을 확인할 수 있다.

하지만 자주 mariadb를 켜고 끄는 상황이라면 brew에서 mariadb를 제어하면 편하다.

## brew에서 mariadb 제어하기

brew에서 mariadb를 제어하는 명령어는 다음과 같다.

```
# Start mariadb
brew services start mariadb

# Show Status brew services
brew services list

# Stop mariadb
brew services stop mariadb
```

위의 명령어가 길기 때문에 zsh의 alias 기능을 활용한다.
























