---
layout: post
title: Java - Big Number: BigInteger, BigDecimal
description: Java - Big Number 다루기
modified: 2016-08-23
tags: [java]
comments: true
image:
  feature: abstract-14.png
---
매우 매우 매우 큰 크기의 숫자를 저장할 수 있는 Java BigInteger와 BigDecimal class에 대해 간단히 알아보자. 

## 1. BigInteger

BigInteger는 거의 무한한 크기의 정수형 숫자를 다루기 위한 것이다.  자세한 내용은 [Java API - BigInteger](https://docs.oracle.com/javase/8/docs/api/java/math/BigInteger.html)을 참고하자. 


### 1.1 사칙 연산

BigInteger constructor의 argument는 string입력이다. 사칙연산을 위해서는 기본 사칙 연산 기호(+-*/%)를 사용할 수 없고 다음의 method를 사용해야 한다. (a, b는 BigInteger instance)

- a.add(b)
- a.subtract(b)
- a.multiply(b)
- a.divide(b)
- a.mod(b)

참고로, 일반 integer를 입력하려면 BigInteger.valueOf(숫자) 형태로 입력한다. 예제는 다음과 같다. 

```java
BigInteger big = new BigInteger("10");
System.out.println(big);
BigInteger anotherBig = big.add(BigInteger.valueOf(100));
System.out.println(anotherBig);
BigInteger theOtherBig = anotherBig.add(big); 
System.out.println(theOtherBig);
```

출력은 다음과 같다. 

```
10
110
120
```

### 1.2 주요 함수들

위의 사칙연산 함수외의 함수는 다음과 같다. 

- 비교: a.compareTo(b) - -1(a<b), 0(a==b), 1(a>b)를 반환
- 같은지 비교: a.equals(b) - true/false 반환
- min: a.min(b)
- max: a.max(b)
- 숫자로 변환: b.doubleValue(), b.floatValue(), b.intValue(), b.longValue()
- 10진법 문자로 변환: b.toString()
- n진법 문자로 변환: b.toString(n)
- 절대값: b.abs()
- n승: b.pow(n)

## 2. BigDecimal

BigDecimal은 무한한 크기의 부동소수점 숫자를 다루기 위한 것이다. [Java API - BigDecimal](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html)을 참고하자 

### 2.1 사칙 연산

BigDecimal도 BigInteger와 거의 **유사한** 방식으로 숫자를 다룬다. 

BigDecimal constructor의 argument는 string입력이다. 사칙연산을 위해서는 기본 사칙 연산 기호(+-*/)를 사용할 수 없고 다음의 method를 사용해야 한다. (a, b는 BigDecimal)

- a.add(b)
- a.subtract(b)
- a.multiply(b)
- a.divide(b,BigDecimal.ROUND_UP) - 소수 첫째 자리에서 올림 (ROUND_DOWN, ROUND_CEILING, ROUND_FLOOR, ROUND_HALF_DOWN/UP/EVEN 등이 있음)
- d.divide(b, n, BigDecimal.ROUND_UP) - 소수점 n자리에서 올림

참고로, 일반 integer를 입력하려면 BigDecimal.valueOf(숫자) 형태로 입력한다. 예제는 다음과 같다. 

```java
BigDecimal b1 = new BigDecimal("0.3);
BigDecimal b2 = new BigDecimal("0.4");
System.out.println(b1.add(BigDecimal.valueOf(0.5)));
System.out.println(b1.multiply(b2));
System.out.println(b1.divide(b2, BigDecimal.ROUND_UP));
System.out.println(b1.divide(b2, 5, BigDecimal.ROUND_UP));
```

출력은 다음과 같다. 

```
0.9
0.12
1.4
1.33334
```
