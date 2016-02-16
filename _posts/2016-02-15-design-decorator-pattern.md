---
layout: post
title: 디자인 - decorator 패턴 
description: decorator 패턴 
modified: 2016-02-15
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---
데코레이터 패턴은 필요에 따라 Object에 동적으로 Responsibility를 추가하는 패턴으로 기능 확장이 필요할 때 상속을 하는 것 대신에 쓸 수 있는 유연한 대안이 된다. 

### 데코레이터 패턴의 필요성

스타벅스를 생각해보자. Beverage Class를 Coffee Class가 상속하고 Coffee Class를 Espresso, Decaf, DarkRoast Class가 상속한다. 

```javascript
class Coffee{
public: 
	virtual double cost() = 0;
	virtual string description() = 0;
};
class Espresso : public Coffee {
};
class Decaf : public Coffee {
};
class DarkRoast : public Coffee {
};
```

다음의 요구사항을 만족시키려면 어떻게 해야 할까?
 
- 스팀 우유나 두유, 모카를 추가하고, 그 위에 휘핑 크림을 얹을 수 있다. 
- 각각 별도의 가격이 책정되어 있다

아마도 엄청나게 많은 조합의 Class를 각기 만들어야 할 것이다. 

```javascript
/* 많은 조합*/
class EspressowithSteamedMilk : public Espresso {
};
class EspressowithSteamedMilkAndMocha : public Espresso {
};
/* ... */
```

Coffee Class를 수정하는 것도 고민해 볼 수 있다. 하지만, 이 경우에도 추가되는 것의 종류가 다양해 진다면 그에 대응해서 계속 Coffee Class를 수정해야 한다는 단점이 있다. 그리고, 특정 첨가물이 포함되면 안되는 종류의 커피가 있거나 더블로 주문하는 것등에 대응하기 어렵다. 

```javascript
class Coffee {
public:
	double cost(){
		if(hasMilk()){
			totalCost += milkCost;
		}
		if(hasMocha()){
			totalCost += mochaCost;
		}
		/* 다양한 토핑 */	
	}
};
```

### 데코레이터 패턴 구현

기본 커피 종류를 선택한 후 첨가물을 추가하는 것 처럼 데코레이터 패턴이 동작한다. 
그렇게 하기 위해 첨가물을 위한 추상 Class를 만들고 Coffee Class를 상속한다. 

```javascript
class Condiment : public Coffee {
};
```

Condiment Class를 상속하여 각 첨가물 Class를 구현하는데 중요한 것은 Constructor의 파라미터로 Coffee Object를 받아서 저장하고 cost()와 description() method에서 이를 이용하는 것이다. 예를 들면 다음과 같다 .

```javascript
class Mocha : public Condiment {
private:
	Coffee *coffee;
public:
	Mocha(Coffee *cof){
		coffee = cof;
	}
	string description(){
		return coffee->description() + " Mocha";
	}
	double cost(){
		return coffee->cost() + 0.20;
	}
};
```

호출은 다음과 같이 Coffee Object와 함께 첨가물 Object를 생성하고, 그 Object를 가리키는 포인터는 Coffee 타입으로 한다. 

```javascript
	Coffee *esp = new Espresso(); //Espresso
	esp = new Mocha(esp); //모카 추가 
	cout << esp.cost();   //2.19
```

### 데코레이터 패턴의 적용

다음을 구현한 풀 코드를 살펴보자. 

- DarkRoast Object를 가져온다. 
- Mocha Object로 장식한다. 
- Whip Object로 장식한다. 
- cost()메소드를 호출해서 가격을 계산한다. 

```javascript
#include <iostream>
using namespace std;
class Coffee {
public:
	virtual double cost() = 0;
	virtual string description() = 0;
};
class Condiment : public Coffee {
};
class Espresso : public Coffee {
public:
	double cost(){
		return 1.99;
	}
	string description(){
		return "Espresso";
	}
};

class Mocha : public Condiment {
private:
	Coffee *coffee;
public:
	Mocha(Coffee *cof){
		coffee = cof;
	}
	string description(){
		return coffee->description() + " Mocha";
	}
	double cost(){
		return coffee->cost() + 0.20;
	}
};

class Whip : public Condiment {
private:
	Coffee *coffee;
public:
	Whip(Coffee *cof){
		coffee = cof;
	}
	string description(){
		return coffee->description() + " Whip";
	}
	double cost(){
		return coffee->cost() + 0.30;
	}
};

int main(){
	Coffee *esp = new Espresso(); //Espresso
	cout << esp->description().c_str() << " " << esp->cost() << endl; //must be "Espresso 1.99"
	esp = new Mocha(esp); //모카 추가 
	cout << esp->description().c_str() << " " << esp->cost() << endl; //must be "Espresso Mocha 2.19"
	esp = new Whip(esp);	//
	cout << esp->description().c_str() << " " << esp->cost() << endl; //must be "Espresso Mocha Whip 2.49"
	esp = new Whip(esp);
	cout << esp->description().c_str() << " " << esp->cost() << endl; //must be "Espresso Mocha Whip 2.79"
	return 0;
}
```
