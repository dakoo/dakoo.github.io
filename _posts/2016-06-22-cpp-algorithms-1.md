---
layout: post
title: c++ - 알고리즘 함수들 (1): sequence 자료 구조의 내용을 수정하지 않는 함수들 
description: c++ - 알고리즘 함수들 (1): sequence 자료 구조의 내용을 수정하지 않는 함수들 
modified: 2015-06-22
tags: [cpp]
comments: true
image: feature: abstract-11.png
---
C++ STL은 자료구조와 함께 강력한 알고리즘들을 제공하고 있다. 그 종류의 풍부함과 호환성은 때로는 사용하기 어렵게 만드는 요인이기도 하다. **<algorithm>** 헤더가 정의하고 있는 다양한 함수들을 살펴보고 정리해 두고자 한다.  

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## sequence 데이터 구조의 내용을 수정하지 않는 함수들

가장 먼저 살펴보고자 하는 함수들은 sequence 자료 구조의 내부를 변경하지 않고도 원하는 값을 얻어내는 유형의 함수들이다. 여기서 말하는 sequence 자료 구조는 STL의 **array, vector, deque, forward_list, list** container들을 말한다. 그 외의 container들인  associative(set, multiset, map, multimap) 및 unordered associative container들(unordered_set, unordered_multiset, unordered_map, unordered_multimap)과는 어떻게 동작하는지도 살펴볼 것이다.

### all_of: 모든 element들이 조건을 모두 만족하는지 확인하는 함수

all_of 함수는 sequence 자료 구조의 모든 element들이 어떤 조건을 모두 만족하는지 확인하는 함수이다. 다음과 같은 prototype을 가진다. 

```cpp
bool all_of (InputIterator first, InputIterator last, UnaryPredicate pred);
```

사용 예는 다음과 같다. 

```cpp
#include <iostream>
#include <algorithm>
#include <array>
#include <vector>
#include <deque>
#include <forward_list>
#include <list>

int main () {
    std::array<int,8> ar{3,5,7,11,13,17,19,23};
    std::vector<int> v{3,5,7,11,13,17,19,23};
    std::deque<int> d{3,5,7,11,13,17,19,23};
    std::forward_list<int> f{3,5,7,11,13,17,19,23};
    std::list<int> l{3,5,7,11,13,17,19,23};
    
    
    if ( std::all_of(ar.begin(), ar.end(), [](int i){return i%2;}) )
        std::cout << "a: All the elements are odd numbers.\n";
    else
        std::cout << "a: some of the elements are even numbers.\n";
    
    if ( std::all_of(v.begin(), v.end(), [](int i){return i%2;}) )
        std::cout << "v: All the elements are odd numbers.\n";
    else
        std::cout << "v: some of the elements are even numbers.\n";
    
    if ( std::all_of(d.begin(), d.end(), [](int i){return i%2;}) )
        std::cout << "d: All the elements are odd numbers.\n";
    else
        std::cout << "d: some of the elements are even numbers.\n";
    
    if ( std::all_of(f.begin(), f.end(), [](int i){return i%2;}) )
        std::cout << "f: All the elements are odd numbers.\n";
    else
        std::cout << "f: some of the elements are even numbers.\n";
    
    if ( std::all_of(l.begin(), l.end(), [](int i){return i%2;}) )
        std::cout << "l: All the elements are odd numbers.\n";
    else
        std::cout << "l: some of the elements are even numbers.\n";
    
    return 0;
}
```

예상 출력은 다음과 같다. 

```
a: All the elements are odd numbers.
v: All the elements are odd numbers.
d: All the elements are odd numbers.
f: All the elements are odd numbers.
l: All the elements are odd numbers.
```

associative 자료 구조와도 잘 동작하는 것을 아래 예를 통해 알 수 있다. 단지, map의 경우 pair를 통해 element를 받아야 한다. 

```cpp
#include <iostream>
#include <algorithm>
#include <set>
#include <map>
#include <deque>
#include <forward_list>
#include <list>

int main () {
    std::set<int> s{3,5,7,11,13,17,19,23};
    std::map<int, int> m{{100,3}, {200,5}, {300, 7}, {101, 11},{102, 13}};
    std::deque<int> d{3,5,7,11,13,17,19,23};
    std::forward_list<int> f{3,5,7,11,13,17,19,23};
    std::list<int> l{3,5,7,11,13,17,19,23};
    
    
    if ( std::all_of(s.begin(), s.end(), [](int i){return i%2;}) )
        std::cout << "s: All the elements are odd numbers.\n";
    else
        std::cout << "s: some of the elements are even numbers.\n";
    
    if ( std::all_of(m.begin(), m.end(), [](std::pair<int, int> p){return p.second%2;}) )
        std::cout << "m: All the elements are odd numbers.\n";
    else
        std::cout << "m: some of the elements are even numbers.\n";
    
...
    return 0;
}
```

예상 출력은 다음과 같다. 

```
a: All the elements are odd numbers.
v: All the elements are odd numbers.
d: All the elements are odd numbers.
f: All the elements are odd numbers.
l: All the elements are odd numbers.
```
