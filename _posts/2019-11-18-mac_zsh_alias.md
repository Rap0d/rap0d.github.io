---
layout: post
title: macOS의 zsh 터미널에서 alias 사용
subtitle: macOS의 zsh 터미널에서 alias 사용
categories: tip
tags: mactip
---

![zsh-logo](/assets/img/logo/zsh-logo.png)

# Overview

macOS의 zsh 터미널에서 alias를 등록하는 과정을 담은 포스트

***

# 사용된 환경

> OS : macOS Catalina 10.15(19B88)  
> zsh : zsh 5.7.1 (x86_64-apple-darwin19.0)  
> 기준 일자 : 2019-11-18  

***

# alias

긴 명령어를 줄임말로 간편하게 사용할 수 있도록 하는 일종의 매크로이다.

예를 들어 *mysql*의 서버를 on/off할때 `mysql.server start`를 입력해야 했다면, alias에 명령어를 추가하여 간단한 단어 조합으로 실행할 수 있다.

***

# 사용

1. mac 터미널 기본 경로에서 다음 명령어를 사용하여 vi 에디터를 통해 `.zshrc` 파일을 생성(혹은 진입)한다.

    ```
    vi .zshrc
    ```

2. 에디터에 들어간 후 `i`를 눌러 *insert* 모드로 변경하고 다음을 타이핑한다.

    이 때, `alias [매크로]="[실제명령어]"`의 포맷으로 입력한다.

    ```
    # mysql server start
    alias ms-start="mysql.server start"

    # mysql server stop
    alias ms-start="mysql.server stop"
    ```

    작성을 끝마쳤으면 `esc` -> `:wq`를 통해 저장한다.

3. vi 편집기에서 빠져나온 후, `source ~/.zshrc`를 타이핑하여 적용한다.

4. 이제 터미널에서 `ms-start`와 `ms-stop`으로 *mysql* 서버를 열고 닫을 수 있게 되었다.