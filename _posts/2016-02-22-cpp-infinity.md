---
layout: post
title: C++ - 무한대 표현
description: infinity
modified: 2016-02-22
tags: [c++]
comments: true
image:
  feature: algorithm.jpeg
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


c++에서 무한대를 표현하기 위해서는 정수형과 그렇지 않은 경우를 나누어서 생각해야 한다. 
모두 header는 `<limits>`이다. 

### 정수형 무한대 

양의 정수 무한대(2,147,483,647)와 음의 정수 무한대(-2,147,483,648)를 다음과 같이 표현한다. 사실 무한대라기 보다는 정수형으로 표현할 수 있는 마지막 수이다. 

- 양의 정수 무한대: numeric_limits<int>::max()
- 음의 정수 무한대: numeric_limits<int>::min()

### 무한대 

실제 무한대를 표시하기 위해서는 float이나 double형이여야 한다. 다음과 같이 표시하면 c++언어가 이해하는 무한대가 표시된다. 

- numeric_limits<float>::infinity()

### 코드 

```javascript
#include <iostream>
#include <limits>
using namespace std;
int main(){
	/* 정수형 */
	cout << numeric_limits<int>::max() << endl; //2,147,483,647
	cout << numeric_limits<int>::min() << endl; // -2,147,483,648
	if (numeric_limits<int>::max() > 100000)
		cout << "Bigger" << endl; //must be printed
	if (numeric_limits<int>::min() < -100000)
		cout << "Smaller" << endl; //must be printed
	/* 진짜 무한대 */
	cout << numeric_limits<float>::infinity() << endl; //1.#INF.
	if (numeric_limits<double>::infinity() > 100000)
		cout << "Bigger" << endl; //must be printed
	return 0;
}
```
