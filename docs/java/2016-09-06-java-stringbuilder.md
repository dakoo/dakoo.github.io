---
layout: post
title: Java - StringBuilder
modified: 2016-09-06
tags: [java]
parent: Java
nav_order: 2
---

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

주의할 점은 index가 2개가 있을 때 뒤의 startIndex는 범위에 포함되고(inclusive) endIndex는 포함되지 않는(exclusive)다는 것이다. 


| Name  |  Description |
|---|---|
| StringBuilder append(String s) | 스트링에 덧붙이기 |
| StringBuilder insert(int offset, String s)|  특정 offset부터 덮어쓰기 |
| StringBuilder replace(int startIndex, int endIndex, String str) | startIndex부터 endIndex까지 str로 대체|
| StringBuilder delete(int startIndex, int endIndex) | startIndex부터 endIndex이전까지 제거|
| StringBuilder reverse() | 스트링 뒤집기 |
| int capacity() | 현재 capacity 획득|
| void ensureCapacity(int minimumCapacity)| 적어도 minimumCapacity까지는 확보하도록 명령|
| char charAt(int index)| 특정 위치의 문자 획득|
| int length()| 길이 획득 |
| String substring(int beginIndex)| beginIndex부터의 substring을 반환|
| String substring(int beginIndex, int endIndex)| beginIndex부터 endIndex까지의 substring을 반환|

### 3.1 append()

- StringBuilder append(String s) : 스트링에 덧붙이기 

```java
StringBuilder sb=new StringBuilder("Hello ");
sb.append("Java");
System.out.println(sb);//Hello Java  
```

### 3.2 insert()

- StringBuilder insert(int offset, String s): 특정 offset부터 덮어쓰기


```java
StringBuilder sb=new StringBuilder("Hello ");
sb.insert(1,"---");
System.out.println(sb);//H---ello 
```

### 3.3 replace()

- StringBuilder replace(int startIndex, int endIndex, String str): startIndex부터 endIndex까지 str로 대체. endIndex는 포함되지 않음. 

insert()가 덮어 쓰는 것에 비해 replace()는 다음의 과정을 수행하는 것이다. 

1. startIndex부터 endIndex이전까지를 잘라내고 그 앞부분과 뒷부분으로 2개의 substring을 만든다.  
2. 사이에 str을 위치시킨다. 
3. 3개의 substring을 합친다. 

```java
StringBuilder one=new StringBuilder("Hello world!");
one.replace(4,7,"---");
System.out.println(one);//Hell---orld!
StringBuilder another=new StringBuilder("Hello world!");
another.replace(4,10,"---");
System.out.println(another);//Hell---d!
StringBuilder third=new StringBuilder("Hello world!");
third.replace(4,8,"---");
System.out.println(third);//Hell--- rld!
```

### 3.4 delete()

- StringBuilder delete(int startIndex, int endIndex) : startIndex부터 endIndex이전까지 제거

```java
StringBuilder sb=new StringBuilder("Hello");
sb.delete(1,3);
System.out.println(sb);//Hlo
```

### 3.5 reverse()

- StringBuilder reverse(): 스트링 뒤집기

```java
StringBuilder sb=new StringBuilder("Hello");
sb.reverse();
System.out.println(sb);// olleH 
```

### 3.6 capacity()

- int capacity(): 현재 capacity 획득


```java
StringBuilder stringBuilder=new StringBuilder();
System.out.println(stringBuilder.capacity()); //16
```

### 3.7 ensureCapacity()

- void ensureCapacity(int minimumCapacity): 적어도 minimumCapacity까지는 확보하도록 명령

만약 현재 capacity보다 클 경우 최소한 (현capacity*2)+2 만큼을 확보한다. 아래의 예를 보면 처음의 ensureCapacity()에서 10은 현재 capacity인 16보다 작은 값이므로 무시한다. 두번째 20은 16보다 크므로 (16*2)+2만큼을 확보한다. 

```java
StringBuilder sb=new StringBuilder();
sb.ensureCapacity(10);
System.out.println(sb.capacity());// 16 - default
sb.ensureCapacity(20);
System.out.println(sb.capacity());// 34
```

### 3.8 charAt()

- char charAt(int index): 특정 위치의 문자 획득

```java
StringBuilder sb=new StringBuilder("Hello");
System.out.println(sb.charAt(4)); //'o'
```

### 3.9 length()

- int length(): 길이 획득 

```java
StringBuilder sb=new StringBuilder("Hello");
System.out.println(sb.length()); //5
```

### 3.10 substring()

- String substring(int beginIndex): beginIndex부터의 substring을 반환
- String substring(int beginIndex, int endIndex): beginIndex부터 endIndex까지의 substring을 반환

```java
StringBuilder sb=new StringBuilder("Hello");
System.out.println(sb.substring(1)); //ello
System.out.println(sb.substring(1,3)); //el
```
