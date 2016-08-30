---
layout: post
title: C++ - multimap
description: C++ - multimap
modified: 2016-02-15
tags: [c++, datastructure]
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


STL의 map은 트리를 이용해 키와 값을 저장하고, unordered_map은 Hashmap을 이용한다. 그러므로 map은 O(logN), unordered_map은 O(1)의 시간 복잡도를 예상할 수 있다.

### STL의 multimap과 unordered_multimap 사용법 

map과 unordered_map은 다음과 같이 간단한 find()를 지원한다.  

```
#include <map>
#include <iostream>
using namespace std;
int main(){
	map<char, int> T;
	T.insert(pair<char, int>('a', 100));
	T.insert(pair<char, int>('b', 200));
	auto it = T.find('b');
	if (it != T.end()) {
		cout << it->second; //must be 200
		T.erase(it);
	}
	return 0;
}
```

multimap과 unordered_multimap은 동일한 키값을 가진 다른 입력 값을 지원하는 자료구조이다. 그러므로 더 복잡한 search 방식을 지원한다. 

- `pair<mutlimap<K,T>::iterator, mutlimap<K,T>::iterator> equal_range(K)`와 같이 key값을 넣어 interator의 쌍을 반환 받는다. 
- `auto it = equal_range(key);` 처럼 단순하게 작성 가능하다. 
- 반환받은 pair의 첫번째 iterator는 시작 아이템, 두번째 iterator는 마지막 아이템의 다음을 가리킨다. 

작성 예는 다음과 같다. 

```javascript
#include <iostream>
#include <map> //multimap이라도 header는 map
using namespace std;
int main(){
	multimap<char, int> T;
	T.insert(pair<char, int>('b', 100));
	T.insert(pair<char, int>('a', 200));
	T.insert(pair<char, int>('b', 300));
	T.insert(pair<char, int>('b', 400));
	auto rt = T.equal_range('b');
	for (auto it = rt.first; it != rt.second; ++it)
		cout << " " << it->second;	//must be 100 300 400
	return 0;
}
```
