---
layout: post
title: C++ - priority_queue
description: C++ - priority_queue
modified: 2016-02-15
tags: [language]
comments: true
image:
  feature: algorithm.jpeg
---
STL의 priority_queue는 default로 max_heap으로 동작하는 자료구조이다. 사용방법을 이해하고 있으면 다양한 상황에 사용가능하다. 

### STL의 priority_queue 사용법

#### 헤더와 지원하는 함수

STL의 priority_queue를 쓰기위해서는 "#include <queue>"를 정의해야 하며 다음의 operation을 지원한다. 

- push() (push_back()이 아니다)
- pop()
- top()
- empty()

clear(), resize()를 지원하지 않으며 constructor를 통해 크기를 정하는 것도 지원하지 않는다. 

#### 사용

##### max_heap

priority_queue는 default가 max_heap이다. 
primitive type인 경우 다음과 같이 사용한다. 

```javascript
priority_queue <int> T;
T.push(1);
T.push(2);
cout << T.top(); //2
```

primitive type이 아닌 경우, 즉 class나 struct의 경우 lessthan operator함수를 이용해서 max_heap을 구현할 수도 있다. 

- operator < (lessthan)의 syntax를 정확히 기억하자. 파라미터와 return이 모두 const여야 하고 <의 방향이 같다. 
- 호출할 때의 syntax도 priority_queue <Item, vector<Item>, less<Item> >이다. 

```javascript
struct Item {
	Item(int lh, int  lw){
		h = lh;
		w = lw;
	}
	/* less operator for max_heap */
	bool operator <(const Item& item) const{ 
		return h*w < item.h * item.w;
	}
	int h;
	int w;
}
int main(){
	/* max_heap with class */
	priority_queue <Item, vector<Item>, less<Item> > X;
	X.push(Item(1, 2));
	/* do something*/
}
```

##### min_heap

priority_queue는 default가 max_heap이므로 min_heap으로 사용하기 위해서는 다음과 같이 사용한다. 
"#include <functional>"과 함께 다음과 같이 사용해야 한다. 

```javascript
#include <functional>
/*...*/
priority_queue <int, vector<int>, greater> T; //less가 아닌 greater이다. 헷갈리수 있다.
T.push(2);
T.push(1);
cout << T.top(); //1
```

primitive type이 아닌 경우, 즉 class나 struct의 경우 lessthan operator함수를 이용해서 max_heap을 구현할 수도 있다. 

- operator > (greaterthan)의 syntax를 정확히 기억하자. 파라미터와 return이 모두 const여야 하고 >의 방향이 같다. 
- 호출할 때의 syntax도 priority_queue <Item, vector<Item>, greater<Item> >이다. 

```javascript
struct compare {
	bool operator()(const int &a, const int &b){
		return a>b;	// 생각한 것과 반대 방향이다!
	}
};
priority_queue <int, vector<int>, compare> T;
T.push(2);
T.push(1);
cout << T.top(); //1
```

### 코드 

```javascript
#include <iostream>
#include <queue>
#include <vector>
#include <functional>
using namespace std;
class Item {
public:
	Item(int lh, int  lw){
		h = lh;
		w = lw;
	}
	/* greater operator for min_heap */
	bool operator >(const Item& item) const{
		return h*w > item.h * item.w;
	}
	/* less operator for max_heap */
	bool operator <(const Item& item) const{
		return h*w < item.h * item.w;
	}
	int getArea(){
		return h*w;
	}
private:
	int h;
	int w;
};
int main(){
	/* default : max_heap with primitive */
	priority_queue <int> T; //default 
	T.push(1);	
	T.push(3);
	T.push(2);
	cout << T.top() << endl; //must be 3
	T.pop();
	cout << T.top() << endl; //must be 2
	/* min_heap with primitive */
	priority_queue <int, vector<int>, greater<int> > R;
	R.push(1);
	R.push(3);
	R.push(2);
	cout << R.top() << endl; //must be 1
	R.pop();
	cout << R.top() << endl; //must be 2

	/* max_heap with class */
	priority_queue <Item, vector<Item>, less<Item> > X;
	X.push(Item(1, 2));
	X.push(Item(3, 4));
	X.push(Item(2, 3));
	cout << X.top().getArea() << endl; // must be 12 (3*4)

	/* min_heap with class */
	priority_queue <Item, vector<Item>, greater<Item> > Y;
	Y.push(Item(2, 3));
	Y.push(Item(1, 2));
	Y.push(Item(3, 4));
	cout << Y.top().getArea() << endl; // must be 2 (1*2)
	return 0;
}
```
