---
layout: post
title: 25. 제네릭 컬렉션 활용 - HashMap
subtitle: 제네릭 컬렉션 활용 - HashMap
categories: study
tags: java
---

![javalogo](/assets/img/logo/java-logo.png)

# Overview 

[제네릭 컬렉션](/study/2019/08/29/java_21_collection/)의 HashMap&lt;K, V&gt;에 대해서 알아본다.

***

# HashMap&lt;K, V&gt;

**HashMap&lt;K, V&gt;** 컬렉션은 경로명이 `java.util.HashMap`이며, **'키(key)'**와 **'값(value)'**의 쌍으로 구성되는 요소를 다룬다.

K는 '키'로 사용할 **데이터 타입**을, V는 '값'으로 사용할 **데이터 타입의 타입 매개 변수**이다.

HashMap(이하 해시맵) 객체의 내부 구성은 다음과 같다.

![HashMap 컬렉션의 내부 구성과 put(), get() 메소드](/assets/img/study/java/190902_fig_1.png "HashMap 컬렉션의 내부 구성과 put(), get() 메소드")

해시맵은 내부에 '키'와 '값'을 저장하는 자료 구조를 각각 가지고 있다.

해시맵에 요소를 삽입하고 검색하기 위해서는 다음 예와 같이 `put(), get()` 메소드를 이용한다.

```java
HashMap<String, String> h = new HashMap<String, String>();  // HashMap 컬렉션 생성
h.put("apple", "사과"); // "apple" 키와 "사과" 값의 쌍을 h에 삽입
String kor = h.get("apple");    // "apple" 키의 값을 검색. kor는 "사과"
```

위 그림에서 `put(key, value)` 메소드는 '키'와 '값'을 받아 '키'를 기반으로 **해시 함수**(hash function)를 실행하여 해시맵 내의 위치를 결정하고, 그 위치에 '키'와 '값'을 각각 저장한다.

`get(key)` 메소드를 호출하면 '키'를 기반으로 해시 함수를 실행하여 '값'이 저장된 위치를 알아내고 '값'을 리턴한다.

해시맵은 삽입 혹은 검색되는 모든 순간에 해시 함수를 통해 '키'와 '값'의 위치를 결정하므로, 어느 위치에 '키'와 '값'이 들어있는지 사용자는 알 수 없으며, 삽입되는 순서와 들어 있는 위치는 관계가 없다.

HashMap&lt;K, V&gt;는 List&lt;E&gt; 인터페이스를 상속받은 Vector&lt;E&gt;나 ArrayList&lt;E&gt;와는 달리 인덱스를 이용하여 요소를 접근할 수 없고 오직 '키'로 검색해야 하므로 요소의 위치나 순서가 중요하지 않은 응용에 많이 사용된다.

HashMap 컬렉션의 주요 메소드는 다음과 같다.

| 메소드 | 설명 |
| :---------- | :---------- |
| void clear() | HashMap의 모든 요소 삭제 |
| boolean containsKey(Object key) | 지정된 키(key)를 포함하고 있으면 true 리턴 |
| boolean containsValue(Object value) | 하나 이상의 키를 지정된 값(value)에 매핑시킬 수 있으면 true 리턴 |
| V get(Object key) | 지정된 키(key)에 매핑되는 값 리턴. 키에 매핑되는 어떤 값도 없으면 null 리턴 |
| boolean isEmpty() | HashMap이 비어 있으면 true 리턴 |
| Set&lt;K&gt; keySet()| HashMap에 있는 모든 키를 담은 Set&lt;K&gt; 컬렉션 리턴 |
| V put(K key, V value) | key와 value를 매핑하여 HashMap에 저장 |
| V remove(Object key) | 지정된 키(key)와 이에 매핑된 값을 HashMap에서 삭제 |
| int size() | HashMap에 포함된 요소의 개수 리턴 |

## HashMap 생성

해시맵은 HashMap&lt;K, V&gt;에서 타입 매개 변수 K와 V에 구체적인 타입을 지정하여 생성한다.

K에는 '키'로 사용할 타입을, V에는 '값'으로 사용할 구체적인 타입을 지정한다.

다음은 영어 단어와 한글 단어의 맵을 만들기 위해 K와 V 모두 *String* 타입을 지정하는 사례이다.

```java
HashMap<String, String> h = new HashMap<String, String>();
```

생성자에 해시맵의 초기 크기를 지정할 수도 있지만, HashMap 역시 자동으로 크기를 조절하므로 크기에 신경 쓰지 않아도 된다.

## HashMap에 요소 삽입

요소를 삽입할 때는 `put()` 메소드에 '키'와 '값'을 인자로 전달한다.

다음에서 첫 번째 인자는 '키', 두 번째 인자는 '값'이다.

```java
h.put("baby", "아기");
h.put("love", "사랑");
h.put("apple", "사과");

String kor1 = h.get("love");    // kor1 = "사랑"
String kor2 = h.get("apple");   // kor2 = "사과"
```

만일 HashMap에 없는 '키'로 `get()` 메소드를 호출하면 `null`을 리턴한다.

다음과 같이 "babo"라는 문자열은 현재 `h`에 없는 '키'이므로 `get("babo")`는 `null`을 리턴한다.

```java
String kor3 = h.get("babo");    // kor3 = null
```

## '키'로 요소 삭제

'키'를 이용하여 요소('값' 포함)를 삭제할 때 다음과 같이 `remove()` 메소드를 이용한다.

```java
h.remove("apple");  // put("apple, "사과")로 삽입한 요소를 삭제함
```

## 요소 개수 알아내기

요소의 개수는 다음과 같이 `size()` 메소드를 호출하면 된다.

```java
int n = h.size();   // 현재 h 내에 있는 요소의 개수 리턴
```

## HashMap을 이용하여 영어 단어와 한글 단어를 쌍으로 저장하고 검색하는 사례

영어 단어와 한글 단어를 쌍으로 HashMap에 저장하고 영어 단어로 한글 단어를 검색하는 프로그램

```java
public class HashMapDicEx {
    public static void main(String[] args) {
        // 영어 단어와 한글 단어 쌍을 저장하는 HashMap 컬렉션 생성
        HashMap<String, String> dic = new HashMap<String, String>();

        // 3개의 (key, value) 쌍을 dic에 저장
        dic.put("baby", "아기");
        dic.put("love", "사랑");
        dic.put("apple", "사과");

        // dic 컬렉션에 들어 있는 모든 (key, value) 쌍 출력
        Set<String> keys = dic.keySet();        // key 문자열을 가진 집합 Set 컬렉션 리턴
        Iterator<String> it = keys.iterator();  // key 문자열을 순서대로 접근할 수 있는 Iterator 리턴
        while (it.hasNext()) {
            String key = it.next();
            String value = dic.get(key);
            System.out.println("(" + key + ", " + value + ")");
        }

        // 사용자로부터 영어 단어를 입력받고 한글 단어 검색
        Scanner sc = new Scanner(System.in);
        for (int i = 0; i < 3; i++) {
            System.out.print("찾고 싶은 단어는? ");
            String eng = sc.next();
            System.out.println(dic.get(eng));
            // '키' eng에 해당하는 '값' 리턴
        }
    }
}
```

## 실행 결과

```
(love, 사랑)
(apple, 사과)
(baby, 아기)
찾고 싶은 단어는? apple
사과
찾고 싶은 단어는? love
사랑
찾고 싶은 단어는? baby
아기
```

## HashMap을 이용하여 자바 과목의 점수를 기록, 관리하는 코드

HashMap을 이용하여 학생의 이름과 자바 점수를 기록, 관리 해보자.

```java
public class HashMapScoreEx {
    public static void main(String[] args) {
        // 사용자 이름과 점수를 기록하는 HashMap 컬렉션 생성
        HashMap<String, Integer> javaScore = new HashMap<String, Integer>();

        // 5개의 점수 저장
        javaScore.put("한홍진", 97);
        javaScore.put("황기태", 34);
        javaScore.put("이영희", 98);
        javaScore.put("정원석", 70);
        javaScore.put("한원선", 99);

        System.out.println("HashMap의 요소 개수 : " + javaScore.size());

        // 모든 사람의 점수 출력. javaScore에 들어 있는 모든 (key, value) 쌍 출력
        Set<String> keys = javaScore.keySet();  // key 문자열을 가진 집합 Set 컬렉션 리턴
        Iterator<String> it = keys.iterator();  // key 문자열을 순서대로 접근할 수 있는 Iterator 리턴

        while (it.hasNext()) {
            String name = it.next();            // 다음 키, 학생 이름
            int score = javaScore.get(name);    // 점수 알아내기
            System.out.println(name + " : " + score);
        }
    }
}
```

## 실행 결과
```
HashMap의 요소 개수 : 5
정원석 : 70
한원선 : 99
한홍진 : 97
이영희 : 98
황기태 : 34 // 출력된 결과는 삽입된 순서와 다르다!!
```

## HashMap을 이용한 학생 정보 저장

`id`와 전화번호로 구성되는 `Student` 클래스를 만들고, 이름을 '키'로 하고 `Student` 객체를 '값'으로 하는 해시맵을 작성

```java
class Student {
    int id;
    String tel;
    public Student(int id, String tel) {
        this.id = id; this.tel = tel;
    }
}

public class HashMapStudentEx {
    public static void main(String[] args) {
        // 학생 이름과 Student 객체를 쌍으로 저장하는 HashMap 컬렉션 생성
        HashMap<String, Student> map = new HashMap<String, Student>();

        // 3명의 학생 저장
        // 이름과 Student 객체를 쌍으로 저장
        map.put("황기태", new Student(1, "010-1111-2222"));
        map.put("한원선", new Student(2, "010-2222-3333"));
        map.put("이영희", new Student(3, "010-3333-4444"));

        System.out.println("HashMap의 요소 개수 : " + map.size());

        // 모든 학생 출력. map에 들어 있는 모든 (key, value) 쌍 출력
        Set<String> names = map.keySet();       // key 문자열을 가진 집합 Set 컬렉션 리턴
        Iterator<String> it = names.iterator(); // key 문자열을 순서대로 접근할 수 있는 Iterator 리턴

        while (it.hasNext()) {
            String name = it.next();    // 다음 키. 학생 이름
            Student student = map.get(name);    // 이름에 해당하는 Student 객체 리턴
            System.out.println(name + " : " + student.id + " " + student.tel);
        }
    }
}
```

## 실행 결과

```
HashMap의 요소 개수 : 3
한원선 : 2 010-2222-3333
이영희 : 3 010-3333-4444
황기태 : 1 010-1111-2222 // 출력된 결과는 삽입된 순서와 다르다!!
```