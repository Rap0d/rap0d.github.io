---
layout: post
title: 1. Database 개요
subtitle: Database 개요
categories: study
tags: database
---

## Overview

Oracle Database의 기초적인 개념에 대한 포스트

***

## 데이터베이스 객체(Object)

- 테이블(Table)
    - 행과 열로 구성된 2차원적인 표

- 시퀀스(Sequence)
    - 정수형 번호표 생성기

- 인덱스(Index)
    - 데이터 검색을 빨리 하기 위하여 만들어 둔 개념

- 프로시저(Procedure)
    - 반환 타입이 ***없는*** 객체

- 함수(Function)
    - 반환 타입이 ***있는*** 객체

### 데이터베이스 객체 현황 보기

- conn / as sysdba (관리자 모드로 로그인 하는 명령어)
- select distinct object_type from dba_objects
- order by object_type;

***

## 데이터베이스와 DBMS

