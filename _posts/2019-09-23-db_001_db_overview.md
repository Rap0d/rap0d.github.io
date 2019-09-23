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

- `conn / as sysdba` (관리자 모드로 로그인 하는 명령어)
- `select distinct object_type from dba_objects`
- `order by object_type;`

***

## 데이터베이스와 DBMS

데이터베이스는 연관된 데이터를 유기적으로 묶어둔 객체를 의미한다.

### 객체

- 데이터베이스를 구성하고 있는 요소들을 의미한다.
- 테이블, 시퀀스, 인덱스, 뷰 등.

### 데이터베이스의 목표

- 지속적인 데이터의 관리 및 보호
- 안전성 보장
- 무결성 보장

### 데이터베이스의 특징

- 실시간 접근이 가능
- 동적인 변화
- 동시에 공용 가능
- 내용에 의한 참조 가능

### DBMS(Database Management System)

- 데이터베이스를 관리하는 시스템 또는 프로그램
- MySQL, MS SQL, Oracle 등

***

## DBMS의 주요 기능

- 데이터의 추가 / 조회 / 변경 / 삭제 기능의 구현
- 데이터의 무결성(신뢰도) 유지되어야 함
- 트랜잭션 관리 기능이 구현되어야 함
- 데이터의 백업 및 복원 가능
- 데이터 보안 기능

### DBMS의 필수 기능

- 정의(Definition)
  - 데이터베이스의 논리, 물리적 구조를 정의하는 기능
- 조작(Manipulation)
  - 검색, 갱신, 삽입, 삭제
- 제어(Control)
  - 정확성과 안전성을 유지하는 제어 기능
