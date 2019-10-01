---
layout: post
title: Visual Studio Code에서 내부 터미널 사용하기
subtitle: Visual Studio Code에서 내부 터미널 사용하기
categories: tip
tags: etctip
---

![vscode](/assets/img/logo/vscode_logo.png "VsCode")

## Overview

Visual Studio Code에서 내부 터미널을 사용할 수 없는 경우 해결방안

***

### 증상

Visual Studio Code에서 내부 터미널을 사용하려 할 때(`Scanner` 등을 사용할 때), `Unrecognized request: { _request: evaluate }` 오류를 출력하며 내부 터미널을 사용할 수 없다.

***

### 해결방안

VS Code의 내부 터미널에 접근하지 못할때는 다음과 같이 해결한다.

> 해당하는 Workspace의 `launch.json` 파일 `"console": "integratedTerminal"` 옵션 추가

위의 절차를 통해 터미널을 VS Code에서 실행할 수 있다.

***

### 원문

#### Accepting inputs

Currently, the VS Code Java Debugger uses the Debug Console as the default console, which doesn't accept input. In order for the console to accept your input (for example when using `Scanner`), you need to change the console to use the Integrated Terminal in `launch.json`.

> "console": "integratedTerminal"

If you'd like to use that setting each time you launch a Java program, you can configure it as a global user setting with java.debug.settings.console.

***

### 참고 문서

- [Visual Studio Documents](https://code.visualstudio.com/docs/java/java-debugging#_accepting-inputs)