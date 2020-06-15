---
layout: post
title: GCP에서 MariaDB 설치 및 세팅하기
subtitle: GCP에서 MariaDB 설치 및 세팅하기
categories: tip
tags: etctip
---

![mariadb-logo](/assets/img/logo/gcp-logo.png){:height="200"}

## Overview

Google Cloud Platform에서 MariaDB 설치와 외부 접속 세팅까지 완료하는 과정을 알아본다.

***

## 사용된 환경

> OS : macOS Catalina 10.15.5(19F101)  
> zsh : zsh 5.7.1 (x86_64-apple-darwin19.0)  
> Linux server : CentOS Linux release 8.1.1911  
> mariadb : 10.4.13  
> 기준 일자 : 2020-06-15  

***

# 작성 예정

### GCP 로그인

### GCP -> VM Instance 생성

> Region : West 1(Oregon)  
> Core : F1 micro machine  
> OS : CentOS 8  
> HDD : 30GB  
> HTTP / HTTPS 보안설정 : Check

### GCP 보안 -> DB 포트(3306)열기

> 모든 인스턴스  
> 수신  
> 0.0.0.0/0

SSH 키 생성 후 Metadata 등록

부팅된 OS에서 firewall 설정(option)

DB 외부접속 User생성

DB 외부 접속 bind address 설정

