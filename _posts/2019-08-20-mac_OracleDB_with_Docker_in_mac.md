---
layout: post
title: macOS에서 Docker를 사용해서 OracleDB 설치하기
subtitle: macOS에서 Docker를 사용해서 OracleDB 설치하기
categories: tip
tags: mactip
---

![oracledb](/assets/img/logo/oracledb_logo.png "OracleDB")
![docker](/assets/img/logo/docker_logo.png "Docker")

# Overview

macOS에서 Docker를 사용하여 OracleDB를 설치하기 위한 절차를 설명하는 문서

OracleDB는 macOS 환경에서 구동되지 않아 리눅스 가상 머신인 Docker를 사용하여 구동한다.

***

# 사용된 환경

> OS : macOS Mojave 10.14.6(18G87)  
> Oracle Database Version : 
> > Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production  
> > PL/SQL Release 11.2.0.2.0 - Production  
> > CORE	11.2.0.2.0	Production  
> > TNS for Linux: Version 11.2.0.2.0 - Production  
> > NLSRTL Version 11.2.0.2.0 - Production  
> 기준 일자 : 2019-8-20  

***

# 설치

다음은 macOS 환경에서 Docker를 설치하고 Oracle Database 11g를 설치하는 절차이다.

## Docker 설치

1. Docker를 설치하기 위해서 Docker에서 회원가입 및 로그인을 하고 [Docker 설치](https://hub.docker.com/editions/community/docker-ce-desktop-mac)에서 다운로드 받는다.  
![dockerdown](/assets/img/tip/mactip/oracle/dockerdown.png "DockerDown")  
2. 상단 Docker의 아이콘을 클릭하여 **Kitematic**을 클릭하여 설치 후 실행한다.  
![kite](/assets/img/tip/mactip/oracle/kitematic.png "Kitematic")  
    - [Kitematic Github](https://github.com/docker/kitematic/releases) 버전이 정상적으로 작동하지 않으면 최신버전을 Github에서 받는다.

## Docker 컨테이너 생성

도커가 개발하는데 편리한 이유 중 하나가 다른 사람들이 미리 만들어놓은 컨테이너를 받아 사용할 수 있다는 것이다.

따로 환경설정하는데 시간을 소비하지 않고 설치하려는 오라클 DB를 검색을 통해 설치할 수 있다.

Oracle xe 11g를 검색하여 보면 다음과 같이 나온다.

![doc1](/assets/img/tip/mactip/oracle/doc_1.png "Doc_1")  

여러 컨테이너가 있지만 라이센스 문제로 내려간 컨테이너가 많아 ***christophesurmont*** 게시자가 올린 컨테이너를 사용한다. (2019년 8월 20일 기준 다운가능)

![doc2](/assets/img/tip/mactip/oracle/doc_2.png "Doc_2")  

다음 메시지가 콘솔에 출력되면 성공한 것이다.

![doc3](/assets/img/tip/mactip/oracle/doc_3.png "Doc_3")  

## Docker 실행 및 포트 확인

해당 포트를 어디에 포워딩했는지는 **Kitematic**에서 해당 컨테이너 세팅에서 볼 수 있다.

![doc5](/assets/img/tip/mactip/oracle/doc_5.png "Doc_5")  

오라클DB의 포트인 1521번 포트가 로컬의 32769번 포트에 포워딩된 것이 확인된다.

## 오라클DB 서버 연결

Docker에 올린 오라클DB 서버와 연결하여 테스팅 하기 위해 **SQL Developer**를 설치한다.

SQL Developer를 받기 전에 PC에 [자바](https://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/index.html)가 설치되어 있어야 한다.

설치완료 후 연결하기 위해 "+" 모양 버튼을 클릭한다.

![doc6](/assets/img/tip/mactip/oracle/doc_6.png "Doc_6")  

연결을 위해 오라클DB서버의 정보를 입력한다.

연결 Name을 지정하고 다음 Default 정보를 입력한다.

> Username : system  
> Password : oracle

포트는 1521포트와 포워딩된 포트를 확인하여 넣어주고 테스트 후 접속한다.

## MacOS Sierra부터 나타나는 Locale not recognized 에러 해결

![sql_error_1](/assets/img/tip/mactip/oracle/sql_error_1.png "sql_error_1") 

해당 에러를 해결하기 위해 Finder에서 `SQLDeveloper.app`을 우클릭 -> 패키지 내용 보기를 클릭한다.

아래와 같은 경로를 따라 `sqldeveloper.conf` 내용을 변경해주어야 한다.

`Contents/Resources/sqldeveloper/sqldeveloper/bin/sqldeveloper.conf`

![sql_error_2](/assets/img/tip/mactip/oracle/sql_error_2.png "sql_error_2") 

해당 파일을 열어 가장 하단에 다음 내용을 추가한다.

```
AddVMOption -Duser.language=ko
AddVMOption -Duser.country=KR
```

설정 완료 후 SQLDeveloper를 재실행하면 한글화가 되어있는 것을 볼 수 있다.

다시 정보를 입력하고 테스트해보면 연결에 성공할 수 있으며 접속 버튼을 클릭하여 진행한다.

***

# 참고 문서

- [맥OS 도커 설치](https://myjamong.tistory.com/105?category=864984)
- [도커를 이용한 맥OS 오라클DB 설치](https://myjamong.tistory.com/106)