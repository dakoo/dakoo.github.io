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

''' javascript
priority_queue <int> T;
T.push(1);
T.push(2);
cout << T.top(); //2
''' 

primitive type이 아닌 경우 compare함수를 이용해서 max_heap을 구현할 수도 있다. 


''' javascript
struct compare {
	bool operator()(const int &a, const int &b){
		return a<b;	// 생각한 것과 반대 방향이다!
	}
};
priority_queue <int, vector<int>, compare> T;
T.push(1);
T.push(2);
cout << T.top(); //2
''' 

##### min_heap

priority_queue는 default가 max_heap이므로 min_heap으로 사용하기 위해서는 다음과 같이 사용한다. 
"#include <functional>"과 함께 다음과 같이 사용해야 한다. 

''' javascript
#include <functional>
/*...*/
priority_queue <int, vector<int>, greater> T; //less가 아닌 greater이다. 헷갈리수 있다.
T.push(2);
T.push(1);
cout << T.top(); //1
''' 

primitive type이 아닌 경우 compare함수를 이용해서 min_heap을 구현할 수도 있다. 

''' javascript
struct compare {
	bool operator()(const int &a, const int &b){
		return a>b;	// 생각한 것과 반대 방향이다!
	}
};
priority_queue <int, vector<int>, compare> T;
T.push(2);
T.push(1);
cout << T.top(); //1
''' 

### 코드 

```javascript
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

struct compare {
	bool operator()(const int &a, const int &b){
		return a > b;	
	}
};
int main(){
	/* default : max_heap */
	priority_queue <int> T; //default
	T.push(1);	
	T.push(3);
	T.push(2);
	cout << T.top() << endl; //3
	T.pop();
	cout << T.top() << endl; //2
	/* min_heap by greater */
	priority_queue <int, vector<int>, compare > R;
	R.push(1);
	R.push(3);
	R.push(2);
	cout << R.top() << endl; //1
	R.pop();
	cout << R.top() << endl; //2

	/* max_heap with another array */
	vector<int> S = { 1, 2, 3, 4, 5, 6, 8 };
	priority_queue <int> X;
	for (auto x : S){
		X.push(x);
	}
	cout << X.top() << endl; //8
	X.pop();
	cout << X.top() << endl; //6

	return 0;
}
```
