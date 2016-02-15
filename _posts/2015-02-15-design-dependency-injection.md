
---
layout: post
title: 디자인 - dependency injection
description: dependency injection
modified: 2016-02-15
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---

Dependency Injection은 하드코딩된 dependency르 제거하고 runtime이나 compile time에 dependency를 변경 가능하게 하는 디자인 패턴이다. 

### 개요

Dependency Injection은 dependency를 내부적으로 포함하는 것 대신 외부에서 dependency를 주입할 수 있도록 object들을 구성하는 표준적이며 중앙집중적인 방법이다. 

다음은 object나 function와 같은 컴포넌트가 dependency를 가지는 방법이다. 

- 컴포넌트가 dependency를 생성한다. 주로 new operator를 이용한다. 
- 컴포넌트가 dependency를 참조한다. 주로 global variable을 이용한다. 
- 필요할때 컴포넌트에 dependency를 전달한다. 

위에서 앞의 2가지 방법은 컴포넌트에 dependency를 하드코딩해야 한다. 세번째 방법은 컴포넌트로부터 dependency에 대한 책임을 제거하는 것이다. 물론 그 dependency는 외부로 옮겨질 뿐 완전히 제거되는 것이 아니다. 


#### Tightly-coupled 클래스들과 Dependency Injection

Driving class는 Vehicle class만이 아니라 Car와 Bike class와 tightly coupled되어 있다. 이로 인해 유지 보수 비용이 상승한다. 

```
#include <iostream>
#include <string>
using namespace std;
class Vehicle{
public:
  virtual void drive() = 0;
};
class Car: public Vehicle{
public:
  void drive() { cout << "driving\n"; }
};
class Bike : public Vehicle
{
public:
  void drive() { cout << "riding\n"; }
};
class Driving
{
public:
  void driveVehicle(string s)
  {
    if(s == "car")
      v = new Car();
    else if( s == "bike")
      v = new Bike();
    v->drive();
  }
private:
    Vehicle *v;
};
```

이를 해결하기 위해 Dependency Injection을 이용해보자. 가장 문제인 driveVehicle method를 다음과 같이 변경한다. Vehicle이 Car인지 Bike인지 결정하는 코드는 다른 class가 담당하게 되고, 이로 인해 dependency가 다른 class에서 inject되게 된다. 

```
  void driveVehicle(Vehicle *v)
  {
     v->drive();
  }

```

dependency를 inject하는 외부의 class는 다음과 같다. 

```
class IDriving
{
public:
  IDriving() { d = new Driving; }
  void drive(string s)
  {
    if(s == "car")
      v = d->driveVehicle(new Car());
    else if( s == "bike")
      v = d->driveVehicle(new Bike());
    else 
      cout << "Need new vehicle";
  }

private:
  Driving *d;
};
```
IDriving interface는 어떤 차량을 달리게 할 지에 따라 수정되어지며, 이를 통해 Driving class는 Vehicle의 종류와 상관없이 동작하게 된다. 

### 타입 

Dependency injection은 class를 더욱 재사용성 높고, test가 쉽고, 다른 것과 decoupling되도록 만든다. dependency를 삽입하기 위한 몇 가지 방법이 있다. 

#### Setter Injection

Dependency를 허용하는 Setter method를 Class에 추가하는 방법

##### 장점

- optional dependency에 유용. dependency가 필요하지 않다면 setter를 호출하지 않는다. 
- 여러 번 호출 가능하다 .

##### 단점

- 언제든지 호출될 수 있으므로 object의 lifetype 내내 depedency가 변경되지 않는다고 확신할 수 없다.  
- setter가 호출되었음을 파악하기 위한 방법이 필요하다. 

#### Constructor Injection 

가장 일반적인 방법이다. class의 constructor를 통해 dependency가 inject되는 것이다. 이것을 위해 constructor에 dependency를 추가하기 위한 파라미터를 추가해야 한다. 

##### 장점

- dependency가 반드시 지켜야할 요구사항이라면 constructor를 통해 반드시 지켜지게 할 수 있다. 
- Lifetime동안 dependency가 변경되지 않음을 확신할 수 있다. 

##### 단점

- optional dependency에는 적합치 않다. 

#### Property Injection

Class의 public member로 노출하고 직접 세팅한다. 별로 좋은 방법은 아니다. 

##### 장점

- 3rd party 라이브러리안에 있는 코드와 작업할 때 유용하다. 

##### 단점

- dependency의 setting을 제어할 수 없다. Lifetime중 어느때라도 변경 가능하다. 

### 참고 

이 글의 대부분은 [여기](http://www.bogotobogo.com/DesignPatterns/Dependency_Injection_Design_Pattern_Inversion_Of_Control_IoC.php)를 참고했다. 


