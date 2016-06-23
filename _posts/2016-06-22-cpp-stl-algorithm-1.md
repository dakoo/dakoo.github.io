---
layout: post
title: cpp - STL 알고리즘 함수들(1)
description: cpp - STL 알고리즘 함수들(1)
modified: 2016-06-21
tags: [cpp]
comments: true
image:
  feature: abstract-11.png
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

## 데이터 구조의 내용을 수정하지 않는 함수들

가장 먼저 살펴보고자 하는 함수들은 자료 구조의 내부를 변경하지 않고도 원하는 값을 얻어내는 유형의 함수들이다. 여기서 말하는 자료 구조는 iterator를 통해 접근할 수 있는 container들로 STL의 sequence container들(**array, vector, deque, forward_list, list**)과  associative(**set, multiset, map, multimap**) 및 unordered associative container들(**unordered_set, unordered_multiset, unordered_map, unordered_multimap**)등이 있다.

### **all_of**: 모든 element들이 조건을 모두 만족하는지 확인하는 함수

**all_of** 함수는 **모든 element들이 어떤 조건을 모두 만족하는지 확인**하는 함수이다. 다음과 같은 prototype을 가진다. first와 last는 자료구조의 범위를 나타내기 위한 iterator이며, **pred**는 조건을 확인하여 bool을 반환하는 함수이다. Lambda를 이용해 구현하도록 하자. 

```cpp
bool all_of (InputIterator first, InputIterator last, UnaryPredicate pred);
```

예제는 다음과 같으며 sequence는 물론이고 associative 자료 구조와도 잘 동작하는 것을 아래 예를 통해 알 수 있다. 즉, **iterator를 통해 접근할 수 있는 자료 구조와 모두 잘 동작한다.** 단지, **map의 경우 pair를 통해** element를 받아야 한다는 것을 명심하자. 

```cpp
#include <iostream>
#include <algorithm>
#include <array>
#include <vector>
#include <deque>
#include <forward_list>
#include <list>
#include <set>
#include <map>
#include <unordered_set>
#include <unordered_map>

int main () {
    std::array<int,8> ar{3,5,7,11,13,17,19,23};
    std::vector<int> v{3,5,7,11,13,17,19,23};
    std::deque<int> d{3,5,7,11,13,17,19,23};
    std::forward_list<int> f{3,5,7,11,13,17,19,23};
    std::list<int> l{3,5,7,11,13,17,19,23};
    std::set<int> s{3,5,7,11,13,17,19,23};
    std::map<int, int> m{{100,3}, {200,5}, {300, 7}, {101, 11},{102, 13}};
    std::unordered_set<int> us{3,5,7,11,13,17,19,23};
    std::map<int, int> um{{100,3}, {200,5}, {300, 7}, {101, 11},{102, 13}};
    
    if ( std::all_of(ar.begin(), ar.end(), [](int i){return i%2;}) )
        std::cout << "a: All the elements are odd numbers.\n";
    
    if ( std::all_of(v.begin(), v.end(), [](int i){return i%2;}) )
        std::cout << "v: All the elements are odd numbers.\n";
    
    if ( std::all_of(d.begin(), d.end(), [](int i){return i%2;}) )
        std::cout << "d: All the elements are odd numbers.\n";
    
    if ( std::all_of(f.begin(), f.end(), [](int i){return i%2;}) )
        std::cout << "f: All the elements are odd numbers.\n";
    
    if ( std::all_of(l.begin(), l.end(), [](int i){return i%2;}) )
        std::cout << "l: All the elements are odd numbers.\n";
    
    if ( std::all_of(s.begin(), s.end(), [](int i){return i%2;}) )
        std::cout << "s: All the elements are odd numbers.\n";
    
    if ( std::all_of(m.begin(), m.end(), [](std::pair<int, int> p){return p.second%2;}) )
        std::cout << "m: All the elements are odd numbers.\n";
    
    if ( std::all_of(us.begin(), us.end(), [](int i){return i%2;}) )
        std::cout << "us: All the elements are odd numbers.\n";
    
    if ( std::all_of(um.begin(), um.end(), [](std::pair<int, int> p){return p.second%2;}) )
        std::cout << "um: All the elements are odd numbers.\n";
    
    return 0;
}
```

위 코드의 출력은 다음과 같다. 

```
a: All the elements are odd numbers.
v: All the elements are odd numbers.
d: All the elements are odd numbers.
f: All the elements are odd numbers.
l: All the elements are odd numbers.
s: All the elements are odd numbers.
m: All the elements are odd numbers.
us: All the elements are odd numbers.
um: All the elements are odd numbers.
```

### **any_of**: 적어도 하나의 element라도 조건을 만족하는지 확인하는 함수

**any_of** 함수는 **적어도 하나의 element라도 어떤 조건을 만족하는지 확인**하는 함수이다. 다음과 같은 prototype을 가진다. first와 last는 자료구조의 범위를 나타내기 위한 iterator이며, **pred**는 조건을 확인하여 bool을 반환하는 함수이다. Lambda를 이용해 구현하도록 하자. all_of와 동일하게 사용할 수 있다.

```cpp
bool any_of (InputIterator first, InputIterator last, UnaryPredicate pred);
```

예제는 다음과 같으며 sequence는 물론이고 associative 자료 구조와도 잘 동작하는 것을 아래 예를 통해 알 수 있다. 즉, **iterator를 통해 접근할 수 있는 자료 구조와 모두 잘 동작한다.** 단지, **map의 경우 pair를 통해** element를 받아야 한다는 것을 명심하자. 

```cpp
#include <iostream>
#include <algorithm>
#include <array>
#include <vector>
#include <deque>
#include <forward_list>
#include <list>
#include <set>
#include <map>
#include <unordered_set>
#include <unordered_map>

int main () {
    std::array<int,8> ar{3,4,7,11,13,17,19,23};
    std::vector<int> v{3,4,7,11,13,17,19,23};
    std::deque<int> d{3,4,7,11,13,17,19,23};
    std::forward_list<int> f{3,4,7,11,13,17,19,23};
    std::list<int> l{3,4,7,11,13,17,19,23};
    std::set<int> s{3,4,7,11,13,17,19,23};
    std::map<int, int> m{{100,3}, {200,4}, {300, 7}, {101, 11},{102, 13}};
    std::unordered_set<int> us{3,4,7,11,13,17,19,23};
    std::map<int, int> um{{100,3}, {200,4}, {300, 7}, {101, 11},{102, 13}};
    
    if ( std::any_of(ar.begin(), ar.end(), [](int i){return i%2;}) )
        std::cout << "a: Any of the elements is an even number.\n";
    
    if ( std::any_of(v.begin(), v.end(), [](int i){return i%2;}) )
        std::cout << "v: Any of the elements is an even number.\n";
    
    if ( std::any_of(d.begin(), d.end(), [](int i){return i%2;}) )
        std::cout << "d: Any of the elements is an even number.\n";
    
    if ( std::any_of(f.begin(), f.end(), [](int i){return i%2;}) )
        std::cout << "f: Any of the elements is an even number.\n";
    
    if ( std::any_of(l.begin(), l.end(), [](int i){return i%2;}) )
        std::cout << "l: Any of the elements is an even number.\n";
    
    if ( std::any_of(s.begin(), s.end(), [](int i){return i%2;}) )
        std::cout << "s: Any of the elements is an even number.\n";
    
    if ( std::any_of(m.begin(), m.end(), [](std::pair<int, int> p){return p.second%2;}) )
        std::cout << "m: Any of the elements is an even number.\n";
    
    if ( std::any_of(us.begin(), us.end(), [](int i){return i%2;}) )
        std::cout << "us: Any of the elements is an even number.\n";
    
    if ( std::any_of(um.begin(), um.end(), [](std::pair<int, int> p){return p.second%2;}) )
        std::cout << "um: Any of the elements is an even number.\n";
    
    return 0;
}
```

각 자료구조의 입력에 '4'가 포함되어 있으므로 위 코드의 출력은 다음과 같다.

```
a: Any of the elements is an even number.
v: Any of the elements is an even number.
d: Any of the elements is an even number.
f: Any of the elements is an even number.
l: Any of the elements is an even number.
s: Any of the elements is an even number.
m: Any of the elements is an even number.
us: Any of the elements is an even number.
um: Any of the elements is an even number.
```
