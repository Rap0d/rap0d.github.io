---
layout: post
title: macOS에서 MySQL 설치하기
subtitle: macOS에서 MySQL 설치하기
categories: tip
tags: mactip
---

![mysql](/assets/img/logo/mysql_logo.png "MySql")

# Overview

macOS에서 MySQL을 사용하기 위한 일련의 절차를 설명하는 문서

# 사용된 환경

> OS : macOS Mojave 10.14.5(18F132)  
> MySQL Version : 8.0.16 for osx10.14 on x86_64 (Homebrew)  
> 기준 일자 : 2019-5-14  

# 설치

1. brew로 mysql을 설치

[brew 설치 방법](https://whitepaek.tistory.com/3)

[brew로 mysql 설치 방법](https://whitepaek.tistory.com/16)

2. cask 설치  
2.1. brew install cask  
2.2. brew list 명령어로 확인  

3. brew update

4. brew search mysql

5. brew install mysql

6. mysql.server start // 서버 실행  
6.1. 네트워크 연결 허용

7. mysql_secure_installation // mysql 설정

```
"Would you like to setup VALIDATE PASSWORD component?
Press y|Y for Yes, any other key for No"
비밀번호 가이드 설정에 대한 질문
Yes 또는 No를 입력

Yes - 복잡한 비밀번호 설정
(ex. "q1w2e3r4"와 같은 복잡한 비밀번호를 설정)
No - 쉬운 비밀번호 설정
(ex. "1234"처럼 쉬운 비밀번호를 설정)
```

```
"Remove anonymous users? (Press y|Y for Yes. any other key for No)"
사용자 설정을 묻는 질문
Yes 또는 No를 입력

Yes - 접속하는 경우 "mysql -uroot"처럼 -u 옵션 필요
No - 접속하는 경우 "mysql"처럼 -u 옵션 불필요

*저는 "Yes"로 설정하였습니다.
```

```
"Disallow root login remotely? (Press y|Y for Yes, any other key for No)"
다른 IP에서 root 아이디로 원격접속을 설정하는 질문

Yes - 원격접속 불가능
No - 원격접속 가능

*저는 "Yes"로 설정하였습니다.
```

```
"Remove test database and access to it? (Press y|Y for Yes, any other key for No)"
Test 데이터베이스를 설정하는 질문

Yes - Test 데이터베이스 제거
No - Test 데이터베이스 유지

*저는 "Yes"로 설정하였습니다.
```

```
"Reload privilege tables now? (Press y|Y for Yes, any other key for No)"
변경된 권한을 테이블에 적용하는 설정에 대한 질문

Yes - 적용시킨다.
No - 적용시키지 않는다.

*해당 질문은 무조건 "Yes" 하시기 바랍니다.
```

8. mysql -uroot -p 명령어를 이용하여 비밀번호를 입력 후 로그인

# MySQL 삭제

1. sudo rm -rf /usr/local/var/mysql 후 mac 비밀번호 입력
2. sudo rm -rf /usr/local/bin/mysql*
3. sudo rm -rf /usr/local/Cellar/mysql
4. 재부팅

# MySQL 명령어

1. MySQL 서버 시작 : mysql.server start
2. MySQL DB 로그인 : mysql -uroot -p
3. MySQL DB 로그아웃 : exit 또는 quit
4. MySQL 서버 종료 : mysql.server stop