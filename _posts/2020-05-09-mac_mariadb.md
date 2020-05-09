---
layout: post
title: macOS에서 Brew로 mariadb 설치하기
subtitle: macOS에서 Brew로 mariadb 설치하기
categories: tip
tags: mactip
---

![mariadb-logo](/assets/img/logo/mariadb-logo.png)

## Overview

macOS에서 Brew로 mariadb 설치하는 과정을 담은 포스트

***

## 사용된 환경

> OS : macOS Catalina 10.15.4(19E287)
> zsh : zsh 5.7.1 (x86_64-apple-darwin19.0)  
> 기준 일자 : 2020-05-09  

***

## Install

사용하는 mac에 brew가 설치되어 있다고 가정한다.

1. brew로 mariadb를 install한다.

```
brew install mariadb
```

2. 설치된 mariadb는 root계정의 비밀번호를 변경해야한다.

```
sudo mariadb-secure-installation
```

sudo를 이용해 진입한다.

3. 초기 root 계정의 비밀번호는 없으므로 Current Password는 엔터를 치고 지나간다.

4. 필요한 mariadb Setting과 비밀번호 초기화를 마치고 mariadb에 진입한다.

```
# mariadb 실행
mysql.server start

# mariadb root 계정 진입
mysql -u root -p
```

5. root 계정에 진입하면 패스워드를 입력한다.