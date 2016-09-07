---
layout: post
title: Java - StringBuilder
description: Java - StringBuilder
modified: 2016-09-06
tags: [java]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## 1. 개요

Java의 String은 immutable한 것이고 StringBuilder와 StringBuffer는 mutable하다. 그리고 StringBuffer는 동기화를 지원하므로 일반적인 상황에서는 StringBulider의 성능이 우세하다. 

MSDN에서는 StringBuilder를 다음과 같이 정의하고 있다. 

> StringBuilder Class represents a mutable string of characters.

## 2. 생성

### 2.1 정의

| Name  |  Description |
|---|---|
| StringBuilder() |빈 StringBuilder - default 크기는 16|
| StringBuilder(int)  |  특정 크기로 생 |
| StringBuilder(String)  | 특정 string으로 생성|

### 2.2 예제

빈 StringBuilder는 크기가 16인데, 16 글자까지 append를 해도 크기가 증가하지 않는다. 

```java
StringBuilder stringBuilder=new StringBuilder();
System.out.println(stringBuilder.capacity()); //16
stringBuilder.append("1234567890123456");
System.out.println(stringBuilder.capacity()); //16
```

하지만, 그 이상의 문자의 경우엔 충분할 만큼 크기를 할당한다. 

```java
StringBuilder stringBuilder=new StringBuilder();
System.out.println(stringBuilder.capacity()); //16
stringBuilder.append("12345678901234567");
System.out.println(stringBuilder.capacity()); //34
```

또한, 문자열과 함께 생성한 경우엔 크기가 다름을 알 수 있다.  

```java
StringBuilder stringBuilder=new StringBuilder("1234567890123456");
System.out.println(stringBuilder.capacity()); //32
```

## 3. 메소드 

| Name  |  Description |
|---|---|
| append(str) | 스트링에 덧붙이기 |
| insert(int, str) |  특정 부터 덮어쓰기 |
| replace(String)  | 특정 string으로 생성|


## 4. Java Stream과 결합 사용 

http://www.javatpoint.com/StringBuilder-class
