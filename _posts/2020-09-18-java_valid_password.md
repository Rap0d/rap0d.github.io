---
layout: post
title: Java에서 비밀번호 검증하기
subtitle: Java에서 비밀번호 검증하기
categories: study
tags: java
---

![javalogo](/assets/img/logo/java-logo.png)

# Overview

웹 서비스를 개발할 때 회원 기능을 구현한다면 비밀번호 검증 기능은 필수로 넣어야한다.

웹 브라우저에서 javascript로 검증을 한 후 서버에 request할 수 있겠지만 변조의 위험으로 인해 서버 사이드(여기서는 Spring Framework)에서 비밀번호를 검증하는 것이 안전하다.

# Environment

> Java Version: zulu openjdk "1.8.0_252"  
> 기준 일자 : 2020-09-18  

# 필요한 기능

안전한 비밀번호는 다음 조건을 충족해야한다.

- 영문 / 숫자 / 특수문자 혼용하여 8자리 이상
- 공란(Blank) 비밀번호 사용 금지
- 단순 비밀번호 사용 금지
  - 3자리 이상 같은 문자(111, aaa)
  - 3자리 이상 연속되는 문자(abc, cba, 123, 321)

이 외에도 사용자 정보의 보안을 위해 비밀번호는 다음 기준을 만족해야한다.

- 변경주기: 3개월에 1회
- 비밀번호 변경 시, 이전 비밀번호 재사용 금지
- 자동저장 금지
- SHA256이상 단방향 암호화
- 20자 이상 SALT 적용
- SSL 기반 전송 암호화
- 비밀번호 분실 시 사용자에 의한 Reset 및 임시비밀번호 발급
- 검증 로직을 서버사이드에 구현

# 구현

이 포스팅에서는 비밀번호 문자열의 검증을 구현한다.

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexTest {

  public static void main(String[] args) {
    String[] testSet = {
        "votmdnj&em123"
        , "kjs@aldkjfklj43"
        , "QBWfklj4543"
        , "abct438983"
        , "acdf@sabcer9182"
        , "alfl234kdd"
        , "asd@fasdf987"
        // Blank 테스트 문자열
        , "xp@tmxm85 84"
        // 공백 테스트 문자열
        , ""
        // 문자 길이 검사를 위한 문자열
        , "OJHDSJK@HFzDLKDJLJoiejwf42^%wij"
        , "xyz47@"
        , "1lkjvneim@"
        // 여기부터 ASCII Overflow 검사를 위한 문자열
        , "/01alkjdffn"
        , "9:;aslkdjfkja2"
        , "?@alakjlkiie3"
        , "Z[\\ekjmvkfd4"
        , "@abieofinv2"
        , "89:8973589723dfasb"
        , "YZ[qoeirnvk235"
    };

    for (String s : testSet) {
      System.out.println("Password: " + s);
      System.out.println(isValidPassword(s));
      System.out.println("--------------------------------");
    }
  }

  /**
   * 비밀번호 검증 메소드
   *
   * @param password 비밀번호 문자열
   * @return 오류 메시지
   */
  public static String isValidPassword(String password) {
    final int MIN = 8;
    final int MAX = 20;

    // 영어, 숫자, 특수문자 포함한 8-20 글자 정규식
    final String REGEX = "^((?=.*\\d)(?=.*[a-zA-Z])(?=.*[\\W]).{" + MIN + "," + MAX + "})$";
    // 3자리 연속 문자 정규식
    final String SAMEPT = "(\\w)\\1\\1";
    // 공백 문자 정규식
    final String BLANKPT = "(\\s)";
    // 정규식 검사객체
    Matcher matcher;

    // 공백 체크
    if (password == null || "".equals(password)) {
      return "No Password";
    }

    // ASCII 문자 비교를 위한 UpperCase
    String tmpPw = password.toUpperCase();
    // 문자열 길이
    int strLen = tmpPw.length();

    // 글자 길이 체크
    if (strLen > 20 || strLen < 8) {
      return "Detect: Incorrect Length(Length: " + strLen + ")";
    }

    // 공백 체크
    matcher = Pattern.compile(BLANKPT).matcher(tmpPw);
    if (matcher.find()) {
      return "Detect: Blank";
    }

    // 비밀번호 정규식 체크
    matcher = Pattern.compile(REGEX).matcher(tmpPw);
    if (!matcher.find()) {
      return "Detect: Wrong Regex";
    }

    // 동일한 문자 3개 이상 체크
    matcher = Pattern.compile(SAMEPT).matcher(tmpPw);
    if (matcher.find()) {
      return "Detect: Same Word";
    } else {
      // 연속된 문자 / 숫자 3개 이상 체크
      int[] tmpArray = new int[strLen];

      // Make Array
      for (int i = 0; i < strLen; i++) {
        tmpArray[i] = tmpPw.charAt(i);
      }

      // Validation Array
      for (int i = 0; i < strLen - 2; i++) {
        // 첫 글자 A-Z / 0-9
        if ((tmpArray[i] > 47
            && tmpArray[i + 2] < 58)
            || (tmpArray[i] > 64
            && tmpArray[i + 2] < 91)) {
          // 3번째 글자 - 2번째 글자 = 1, 3번째 글자 - 1번째 글자 = 2
          if (Math.abs(tmpArray[i + 2] - tmpArray[i + 1]) == 1
              && Math.abs(tmpArray[i + 2] - tmpArray[i]) == 2) {
            char c1 = (char) tmpArray[i];
            char c2 = (char) tmpArray[i + 1];
            char c3 = (char) tmpArray[i + 2];
            return "Detect: Continuous Pattern: \"" + c1 + c2 + c3 + "\"";
          }
        }
      }
      // Validation Complete
      return ">>> All Pass";
    }
  }
}
```

# 결과

```
Password: votmdnj&em123
Detect: Continuous Pattern: "123"
--------------------------------
Password: kjs@aldkjfklj43
>>> All Pass
--------------------------------
Password: QBWfklj4543
Detect: Wrong Regex
--------------------------------
Password: abct438983
Detect: Wrong Regex
--------------------------------
Password: acdf@sabcer9182
Detect: Continuous Pattern: "ABC"
--------------------------------
Password: alfl234kdd
Detect: Wrong Regex
--------------------------------
Password: asd@fasdf987
Detect: Continuous Pattern: "987"
--------------------------------
Password: xp@tmxm85 84
Detect: Blank
--------------------------------
Password: 
No Password
--------------------------------
Password: OJHDSJK@HFzDLKDJLJoiejwf42^%wij
Detect: Incorrect Length(Length: 31)
--------------------------------
Password: xyz47@
Detect: Incorrect Length(Length: 6)
--------------------------------
Password: 1lkjvneim@
Detect: Continuous Pattern: "LKJ"
--------------------------------
Password: /01alkjdffn
Detect: Continuous Pattern: "LKJ"
--------------------------------
Password: 9:;aslkdjfkja2
>>> All Pass
--------------------------------
Password: ?@alakjlkiie3
>>> All Pass
--------------------------------
Password: Z[\ekjmvkfd4
>>> All Pass
--------------------------------
Password: @abieofinv2
>>> All Pass
--------------------------------
Password: 89:8973589723dfasb
>>> All Pass
--------------------------------
Password: YZ[qoeirnvk235
>>> All Pass
--------------------------------
```

정상적으로 비밀번호 검증이 완료된 모습을 볼 수 있다.