---
layout: post
title: Mac OS에서 Docker를 사용해서 OracleDB 설치하기
subtitle: Mac OS에서 Docker를 사용해서 OracleDB 설치하기
categories: tip
tags: tip mactip
---

![oracledb](/assets/img/tip/mactip/oracle/oracledb_logo.png "OracleDB")
![docker](/assets/img/tip/mactip/oracle/docker_logo.png "Docker")

##  Overview

Mac OS에서 Docker를 사용하여 OracleDB를 설치하기 위한 절차를 설명하는 문서

OracleDB는 Mac OS 환경에서 구동되지 않아 리눅스 가상 머신인 Docker를 사용하여 구동한다.

***

## 사용된 환경

OS : Mac OS Mojave 10.14.6(18G87)

Oracle Database Version : 

> Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production  
> PL/SQL Release 11.2.0.2.0 - Production  
> CORE	11.2.0.2.0	Production  
> TNS for Linux: Version 11.2.0.2.0 - Production  
> NLSRTL Version 11.2.0.2.0 - Production  

기준 일자 : 2019년 8월 20일

***

## 설치

다음은 Mac OS 환경에서 19c Docker 인스턴스를 만드는데 사용되는 절차이다.

1. Docker를 설치하기 위해서 Docker에서 회원가입 및 로그인을 하고 [Docker 설치](https://hub.docker.com/editions/community/docker-ce-desktop-mac)에서 다운로드 받는다.  
![dockerdown](/assets/img/tip/mactip/oracle/dockerdown.png "DockerDown")  
2. [Oracle 다운페이지](http://edelivery.oracle.com/)에서 로그인을 한 후 **Oracle Database**를 검색하여 해당하는 버전(*19.3.0.0.0 for Linux x86-64*)의 파일(V982063-01.zip)을 다운로드 받는다.