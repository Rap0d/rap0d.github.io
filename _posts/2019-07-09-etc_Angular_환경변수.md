---
layout: post
title: Angular2에서 환경변수 설정하기
subtitle: Angular2에서 환경변수 설정하기
categories: tip
tags: etctip
---

![angular](/assets/img/tip/etctip/angular.png "Angular")

## Overview

Angular2를 사용할 때 Eclipse IDE에서 환경변수가 잡히지 않는 경우가 간혹 있다.

***

### 증상

`~/.bash_profile`이 Eclipse 터미널에서 열리지 않는 현상

***

### 해결방안

Eclipse의 내부 터미널에 Augular2가 접근하지 못할때는 다음과 같이 해결한다.

> Eclipse Preferences -> Terminal -> Local Terminal
>
> Arguments 값에 `--login` 입력

위의 절차를 통해 `~/.bash_profile`을 Eclipse의 터미널에서 실행할 수 있다.

***

#### 원문

```
For those of you looking for an answer years later (Neon, Oxygen):
Some of my node and angular/angular2 tooling in eclipse failed due to missing $PATH entries in the MacOS terminal. Your tooling propably utilizes the embedded eclipse terminal which does not start providing your login/user shell. So you need to set the eclipse terminal in your eclipse preferences to start as --login shell in order to comprise your users PATH settings:
Go to:
Preferences -> Terminal -> Local Terminal
and set
Arguments to: --login
open a new Terminal inside Eclipse and your user's $PATH should be used from now on. Also everything you have set up in ~/.bash_profile will run when opening a new Terminal in Eclipse.
```