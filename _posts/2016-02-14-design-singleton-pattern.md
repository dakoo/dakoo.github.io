---
layout: post
title: 디자인 - singleton pattern
description: singleton pattern
modified: 2016-02-14
tags: [design]
comments: true
image:
  feature: algorithm.jpeg
---
c++로 구현한 간단한 singleton 패턴. 

### 디자인 

- constructor와 destructor는 private이여야 하고 getInstance()는 public인데 static으로 선언되어 어디서나 호출가능해야 한다. 
- 단 하나의 object만을 유지해야 하므로 객체의 포인터를 private이며 static 멤버로 한다.
- Object를 가리키는 포인터는 NULL로 초기화되어 있어야 한다. 
- getInstance()가 호출되면 object의 포인터를 확인해서 NULL이 아니면 포인터를 그대로 반환하고 NULL이면 객체를 생성한 후 반환한다. 
- DestroyInstance()의 경우는 반대로 instance가 NULL이 아니면 delete하고 NULL로 만든다. 

### 코드 

```java
#include <iostream>
using namespace std;
class Singleton
{
public:
	static Singleton *getInstance(){
		if (!instance){
			cout << "instance is created" << endl;
			instance = new Singleton();
		}else {
			cout << "instance has been already created" << endl;
		}
		return instance;
	}
	static void destroyInstance(){
		if (!instance){
			cout << "instance has been already removed" << endl;
		}else {
			delete instance;
			cout << "instance is removed" << endl;
		}
		instance = NULL;
	}

private:
	Singleton(){};
	~Singleton(){};
	static Singleton* instance;
};
Singleton* Singleton::instance = NULL;
int main(){
	Singleton *s1 = Singleton::getInstance();//instance is created
	Singleton *s2 = Singleton::getInstance();//instance has been already created
	s1->destroyInstance(); //instance is removed
	s2->destroyInstance(); //instance has been already removed
	return 0;
}
```
