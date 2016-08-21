---
layout: post
title: Design - OOD 초간단 접근법
description: OOD 초간단 접근법
modified: 2016-02-16
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---

주어진 요구사항이 주어졌을때 아주 간단히 OOD를 적용해 보는 초간단 접근법이다. 제대로 하기 위해서는 많은 iteration과 분석이 필요하지만 간단히 해보는 능력을 가지는 것도 필요하다. 여기서는 식당 예약 시스템을 예로 들어보자.  

### 초간단 접근법

#### 메인 유저 파악 

가장 중요한 것은 메인 유저가 누구인지 파악하는 것이다. 여러 타입의 유저(예를 들어 일반 사용자, 관리자 등등)가 있다면 그만큼 힘들다. 그런 경우라도 일단 메인 유저에 집중하자.  

#### 시스템 Scope과 이를 둘러싼 환경 및 제약 파악

위에서 파악한 메인 유저가 모두 균등한지, 아니면 권한이 다른지 파악한다. 시스템 입력과 출력의 종류가 무엇인지, 몇 개나 있을 수 있는지 파악해야 한다. 

#### 주어진 요구사항 확인

메인 유저가 사용해야 하는 기능을 도출한다. 이때 도출된 기능이 보통 시스템의 인터페이스로 사용된다. 이 단계에서는 상세한 인터페이스의 아규먼트나 기능을 상세히 도출할 필요는 없다. 

#### 요구 사항 상세화

도출한 기능을 각 유즈케이스 별로 상세화 해야 한다. 이렇게 하기 위해서는 더 자세한 질문과 답이 필요하다. 때로는 스스로에게 질답을 해서 명확히 해야 한다. 상세화된 요구사항이 도출됨에 따라 두루뭉실하게 도출된 인터페이스의 아규먼트와 리턴이 결정된다. 
예를 들어 식당의 예약기능이라면 이라면 특정 테이블을 예약해야 하는 것인지가 상세화 되어야 한다.  그에 따라 makeReservation의 아규먼크가 makeReservation(예약자, 사람수, 시간), makeReservation(예약자, 사람수, 시간, 테이블 위치), makeReservation(예약자, 사람수, 시간, 테이블 위치, 특별한 주문)등이 나타난다.  

#### 인터페이스 확정

전체 시스템의 인터페이스를 확정한다. enum Key{}; class Time {}; class Money {}; 등을 기본 class로 사용한다. 
 
####  Class를 도출하며 Class Diagram을 그리기

##### Class

Class는 다음과 같이 도출한다. 

- 시스템 인터페이스에서 입력, 출력으로 도출된 Class들을 표시한다. 
- 추상화가 필요한 것을 뽑는다. 여러가지 종류가 있는 것이 보통 그렇다.  
- 검색이 필요한 것은 id(key)를 멤버로 추가하고 map을 자료구조로 이용한다. 
- map등의 자료구조를 가진 것은 보통 aggregation 또는 composition 관계인 클래스이다. 
- Aggregation관계인 것은 add, delete 등의 method를 추가한다. 
- public/private을 뽑는다. 

##### Class Relationship

Class Diagram을 그려서 표현하는데 다음과 같은 Class간 관계를 사용한다. 

- 삼각형 실선 화살표: 상속
- 삼각형 점선 화살표: Realization (interface나 abstract class)
- 점선 화살표: dependency(parameter나 return)
- 실선 (화살표): association(member)
- 검정사각형 실선(화살표): composition
- 하얀사각형 실선(화살표): aggregation

##### Pattern  적용

Pattern을 적용하기 용이할지 파악한다. 대표적으로 사용할 만한 패턴은 다음과 같다. 

- Singleton(private constructor, static pointer와 getInstance)
- Observer (add, set, notify), Observable (update)
- Decorator(객체 생성 시 자기 부모 type을 넣고 멤버로 저장, override된 함수 호출 시 자기 자신 결과 추가해서 반환)

Decorator 패턴의 호출 예는 다음과 같다. 

```
	Coffee *esp = new Espresso(); //Espresso
	esp = new Mocha(esp); //모카 추가 
	cout << esp.cost();   //2.19 
```

#### 프로토타이핑 

인터페이스 중심으로 작성하며 제일 중요해 보이는 인터페이스는 구현을 한다. 
