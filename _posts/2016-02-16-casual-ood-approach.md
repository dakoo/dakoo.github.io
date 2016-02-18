---
layout: post
title: 디자인 - OOD 초간단 접근법
description: OOD 초간단 접근법
modified: 2016-02-16
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---

주어진 요구사항이 주어졌을때 아주 간단히 OOD를 적용해 보는 초간단 접근법이다. 제대로 하기 위해서는 많은 iteration과 분석이 필요하지만 간단히 해보는 능력을 가지는 것도 필요하다. 

### 초간단 접근법

#### 주어진 요구사항 확인

가장 중요한 것은 메인 유저가 누구인지 파악하는 것이다. 여러 타입의 유저(예를 들어 일반 사용자, 관리자 등등)가 있다면 그만큼 힘들다. 
그런 경우라도 일단 메인 유저에 집중하자. 그런 후 메인 유저가 사용해야 하는 기능을 도출한다.  이때 도출된 기능이 보통 시스템의 인터페이스로 사용된다. 이 단계에서는 인터페이스의 아규먼트나 기능 

2. use case 상세화
각 유즈케이스 별로 상세화 해야 한다. 보통 상세화된 것에 따라 위에서 정의한 interface의 argument가 결정된다. 
예를 들어 make a reservation이라면 특정 table을 예약해야 하는 것인지가 상세화 되어야 한다.  그에 따라 makeReservation의 argument가 바뀐다. 

3. Feature 도출
위의 내용을 가지고 전체 시스템을 표현하는 클래스의 interface를 확정한다. 
 
4. Class Diagram을 그리며 다른 Class를 도출한다. 

Class 표현법: 
----------------
 <<interface>>
이름 
--------------
- 이름 : type 
+ 이름 : type {readOnly}
--------------
- 이름() : type 
+ 이름() : type
--------------
삼각형 실선 화살표: 상속
삼각형 점선 화살표: Realization (interface나 abstract class)
점선 화살표: dependency(parameter나 return)
실선 (화살표): association(member)
검정사각형 실선(화살표): composition
하얀사각형 실선(화살표): aggregation

- 가장 기본이 되는 단위부터 큰 것 까지 그리며 추상화가 필요한 것은 Class로 아닌 것은 enum으로 한다.
대표적으로 Time(시간), Button(입력), Money(돈) 등을 enum으로 표현할 수 있다. 
- 추상화가 필요한 것을 뽑는다. 여러가지 종류가 있는 것이 보통 그렇다.  
- 검색이 필요한 것은 id(key)를 멤버로 추가하고 map을 자료구조로 이용한다. 
- map등의 자료구조를 가진 것은 보통 aggregation 또는 composition 관계인 클래스이다. 
- Aggregation관계인 것은 add, delete 등의 method를 추가한다. 
- Composition관계인 것은 add, delete 등이 나와있지 않다. 
- public/private을 뽑는다. 

5. prototype code를 만든다. 인터페이스 중심으로 작성한다.

- 가장 작은 단위의 클래스부터 만들어 나간다. 
- 큰 시스템은 가장 마지막에 작성한다. 이때 시간이 된다면 맨 처름에 정의한 interface의 내부를 구현까지 한다. 

6. Pattern
Singleton(private constructor, static pointer and getInstance), Observer (add, set, notify... Observable: update()),
Decorator(상속 후, 객체 생성 시 자기 부모 type을 넣고 멤버로 저장, override된 함수 호출 시 자기 자신 결과 추가해서 반환)
Coffee *esp = new Espresso(); //Espresso
	esp = new Mocha(esp); //모카 추가 
	cout << esp.cost();   //2.19 
