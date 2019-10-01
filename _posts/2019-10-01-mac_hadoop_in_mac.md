---
layout: post
title: Mac OS에서 Hadoop 설치하기
subtitle: Mac OS에서 Hadoop 설치하기
categories: tip
tags: mactip
---

![hadoop](/assets/img/logo/hadoop-logo.png "hadoop")

## Overview

Mac OS에서 Hadoop(하둡)을 설치하기 위한 절차를 설명하는 문서

***

## 사용된 환경

OS : Mac OS Mojave 10.14.6(18G103)

Hadoop : 3.2.1

기준 일자 : 2019년 10월 1일

***

## 설치

Mac OS에서는 ***brew***를 이용하여 쉽게 설치할 수 있다.

혹시 *brew*가 설치되지 않았으면 [brew 설치 방법](https://whitepaek.tistory.com/3)을 참고한다.

터미널에서 다음 명령어를 통해 하둡을 설치한다.

```
brew install hadoop
```

만약 다음과 같은 에러가 발생한다면 Finder 에서 `/usr/local/sbin` 폴더를 생성하면 해결된다.

```
" Error: The `brew link` step did not complete successfully. The formula built, but is not symlinked into /usr/local. Could not symlink sbin/FederationStateStore. /usr/local/sbin is not writable."
```

***

## 하둡 설정

하둡 설정을 위해 관련된 파일을 수정해줘야 한다.

수정 해야 하는 파일은 `/usr⁩/⁨local⁩/Cellar⁩/hadoop⁩/x.x.x/libexec⁩/etc` 위치의 4개 파일이다.

> hadoop-env.sh  
> Core-site.xml  
> mapred-site.xml  
> hdfs-site.xml

- hadoop-env.sh

    ```
    기존: export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true"
    변경: export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="  
    ```
    - 해당하는 파일의 기존 내용이 없다면 변경에 해당하는 내용을 추가로 기재한다.

- Core-site.xml

    ```
    <configuration>
        <property>
            <name>hadoop.tmp.dir</name>
            <value>/usr/local/Cellar/hadoop/hdfs/tmp</value>
            <description>A base for other temporary directories.</description>
        </property>
        <property>
            <name>fs.default.name</name>
            <value>hdfs://localhost:9000</value>
        </property>
    </configuration>
    ```

    - 파일의 `configuration` 태그 안에 작성한다.

- mapred-site.xml
  
    ```
    <configuration>
        <property>
            <name>mapred.job.tracker</name>
            <value>localhost:9010</value>
        </property>
    </configuration>
    ```

    - 파일의 `configuration` 태그 안에 작성한다.

- hdfs-site.xml

    ```
    <configuration>
        <property>
            <name>dfs.replication</name>
            <value>1</value>
        </property>
    </configuration>
    ```

    - 파일의 `configuration` 태그 안에 작성한다.

***

## 하둡 실행

하둡을 실행하기 전에 하둡 파일 시스템(HDFS)으로 포맷을 해야한다.

터미널에서 다음과 같이 입력하여 HDFS로 포맷한다.

```
hdfs namenode -format
```

포맷 후, 아래와 같이 *ssh key*를 생성하고 사용한다.

이 때, `ssh-keygen -t rsa` 를 실행 시킨 후 *key* 이름과 비밀번호 입력 라인이 나올때, 빈칸으로 엔터를 누르면 자동으로 `id_rsa.pub`이 생성된다.

```
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

다음과 같이 실행하면 하둡을 띄울 수 있다.

```
/usr/local/Cellar/hadoop/x.x.x/sbin/start-dfs.sh
>> 이 때, x.x.x는 버전
```

다음과 같은 에러는 원격 로그인을 허용하지 않았을 경우 발생한다.

```
"localhost: ssh: connect to host localhost port 22: Connection refused"
```

환경설정의 **공유**에 들어가서 원격 로그인을 허용한다.

![hadoopfix01](/assets/img/tip/mactip/hadoop/hadoop-fig01.png "hadoopfix01")

### 실행 및 종료 명령어

하둡을 실행하고 종료하는 명령어는 다음과 같다.

> 실행  
> `/usr/local/Cellar/hadoop/x.x.x/sbin/start-dfs.sh`  
> `/usr/local/Cellar/hadoop/x.x.x/sbin/start-yarn.sh`  
> 종료  
> `/usr/local/Cellar/hadoop/x.x.x/sbin/stop-dfs.sh`  
> `/usr/local/Cellar/hadoop/x.x.x/sbin/stop-yarn.sh`  

정상적으로 실행된 것인지 알고싶을 때 `jps` 명령어를 입력한다.

![hadoopfix02](/assets/img/tip/mactip/hadoop/hadoop-fig02.png "hadoopfix02")

### 하둡 확인

*localhost*로 하둡을 띄운것이기 때문에 *localhost*로 접속해서 하둡의 상태를 체크할 수 있다.

- Cluster status: http://localhost:8088  
![hadoopfig03](/assets/img/tip/mactip/hadoop/hadoop-fig03.png "hadoopfig03")
- HDFS status: http://localhost:9870  
![hadoopfig04](/assets/img/tip/mactip/hadoop/hadoop-fig04.png "hadoopfig04")
- Secondary NameNode status: http://localhost:9868  
![hadoopfig05](/assets/img/tip/mactip/hadoop/hadoop-fig05.png "hadoopfig05")

***

## 참고 문서

- [맥(mac)에서 하둡(hadoop) 설치하기](https://tariat.tistory.com/492)