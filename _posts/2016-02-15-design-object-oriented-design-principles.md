---
layout: post
title: Design - Object Oriented Design Principles
description: Object Oriented Design Principles
modified: 2016-02-15
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---

대표적인 Object Oriented Design Principle은 SOLID이다. SOLID는 다음의 원칙들을 나타낸다. 

-	S: Single Responsibility Principle
-	O: Open-Closed Principle
-	L: Liskov Substitution Principle
-	I: Interface segregation Principle
-	D: Dependency Inversion Principle

그 외에 DRY도 이해하기 쉬운 Principle로 꼽히고 있다. 

-	DRY: Don’t Repeat Yourself Principle

그 내용에 대해 간단히 정리해보자. 

### Single Responsibility Principle (SRP) 

-	하나의 모듈, 그러니까 하나의 클래스 혹은 하나의 메소드는 하나의 일에 대해서만 책임을 질 수 있도록 잘 분리되어 있어야 한다는 의미이다.  
-	객체 지향의 지향점 중 하나인 높은 응집도(High Cohesion)를 말한다.  
-	사고(logic)와 표현(Presentation)의 분리뿐 아니라, 관심도 철저하게 분리(Separation of Concerns)되어야 한다고 이야기 하고 있다. 
-	SRP가 잘 되어 있는 모듈은 중복(Duplication)이 적으며, 변경이 한 곳에서만 일어날 수 있게 만들어 준다. 

#### Don’t Repeat Yourself Principle (DRY) 

-	SOLID에 속하지 않지만, 중복된 코드에 대한 것으로 SRP와 매우 유사한 원칙이다. 
-	중복 코드를 피하라는 의미는 하나의 위치에서는 하나의 요구사항만 다루라는 것이다. 이를 위해 공통 부분은 추상화하고, 같은 위치에 두어야 한다. 

### Open-Closed Principle (OCP)

-	변경에 대한 것으로 확장에 열려있고 코드 수정에는 닫혀있어야 한다는 법칙이다. 
-	디자인 패턴들의 기본 원칙이 되는 원칙이다. 다형성과 인터페이스를 통해 변하는 부분과 변하지 않는 부분을 분리해 낸다. 

### Liskov Substitution Principle (LSP) 

-	상속에 대한 것이다. 
-	'자식(Sub-type)은 부모(Super-type)로 치환될 수 있다' 는 원칙이다.
-	예를 들어 animal – dog 상속 관계에서 Animal A = new Dog(); 가 가능해야 한다는 이야기이다.
-	상속을 할 때 서브 클래스로 베이스 클래스를 대신해도 문제가 없도록 해야 한다. LSP가 제대로 되지 않으면 상속구조에 문제가 있는 것으로 혼란스러운 코드를 만들게 된다. 
-	Matix와 3DMatrix가 있을 때 Matrix가 2D(x,y)만 정의하고 있다면 LSP에 위배된다. 이때, 3DMatrix와 Matrix 관계는 상속이 아니라 3DMatrix가 Matrix를 delegation하는 관계가 적당하다. 

### Interface segregation Principle (ISP)

-	Fat interface에 대한 것이다. 
-	Class의 interface는 한 종류의 클라이언트를 명세하도록 정의되어야 한다. 

### Dependency Inversion Principle (DIP)

-	추상화에 관한 것. 
-	"상위 레벨의 모듈은 하위 레벨들에 의존해선 안되며, 모든 것들은 추상에 의존해야 한다"는 원칙이다. 
-	쉽게는 인터페이스에 의한 코딩으로 해석해도 무방하다. 
-	상위 모듈과 하위 모듈은 각기 독립적이며, interface에만 의존적이다. 이를 통해 각기 구현 및 테스트가 가능해진다. 
-	추상은 구현에 의존적이지 않다. 구현은 추상(interface)에 의존적이다. 

#### Example

다음은 Button class와 Lamp class가 tightly-coupled 되어 있다. 이것은 DIP를 위배하고 있는 것이다. 왜냐하면 Button class가 직접적으로 Lamp에 의존하고 있기 때문이다. Lamp가 변경이 생기면 Button의 변경이 필요하고, 또한 Button을 Lamp만이 아니라 Motor를 위해 재 사용할 수 없다. 

```javascript
	class Button {
	private:
		Lamp lamp;
	public:
		void Poll(){
			if (/*some condition*/)
				lamp.turnOn();
		}
	};
```

중간에 Switchable Interface를 이용해 Button의 Lamp dependency를 invert할 수 있다. Button의 인터페이스를 제공하는 Switchable을 통해 on/off를 제어하고, Lamp는 Switchable interface에 따라 구현한다. 

```javascript
class Switchable {
public:
	virtual void turnOn() = 0;
	virtual void turnOff() = 0;
};
class Lamp : public Switchable {
public:
	void turnOn(){ /*…*/ };
	void turnOff(){ /*…*/ };
};

class Button {
private:
	Switchable sw;
public:
	void Poll(){
		if (/*some condition*/)
			sw.turnOn();
	}
};
```

위처럼 Lamp는 Switchable에 의존적인 구현을 하게 되지만, Button은 Switchable 구현에 의존적이지 않게 된다. 기존에 Button이 Lamp에 의존적인 구현을 했던 것을 생각해보면, Dependency가 뒤집힌(Dependency Inversion) 것을 알 수 있다.  

