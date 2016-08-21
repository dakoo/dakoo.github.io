---
layout: post
title: C++ - STL all_of, any_of, none_of 
description: C++ STL 
modified: 2015-06-22
tags: [c++]
comments: true
image:
  feature: abstract-11.png
---
C++ STL은 자료구조와 함께 강력한 알고리즘들을 제공하고 있다. 그 종류의 풍부함과 호환성은 때로는 사용하기 어렵게 만드는 요인이기도 하다. 그 중 c++11에 추가된 all_of, any_of, none_of를 살펴보자. 

### all_of, any_of, none_of

all_of, any_of, 그리고 none_of는 자료 구조내의 element들이 어떤 조건을 만족하는지 확인하는 함수이다. 아래 그 구현을 보면 알수 있다시피 구현이 대동소이하고, 성능도 대동소이하다. 

```cpp
template<class InputIterator, class UnaryPredicate>
  bool all_of (InputIterator first, InputIterator last, UnaryPredicate pred)
{
  while (first!=last) {
    if (!pred(*first)) return false;
    ++first;
  }
  return true;
}

template<class InputIterator, class UnaryPredicate>
  bool any_of (InputIterator first, InputIterator last, UnaryPredicate pred)
{
  while (first!=last) {
    if (pred(*first)) return true;
    ++first;
  }
  return false;
}

template<class InputIterator, class UnaryPredicate>
  bool none_of (InputIterator first, InputIterator last, UnaryPredicate pred)
{
  while (first!=last) {
    if (pred(*first)) return false;
    ++first;
  }
  return true;
}
```

#### all_of: 모든 element들이 조건을 모두 만족하는지 확인하는 함수

all_of 함수는 모든 element들이 어떤 조건을 모두 만족하는지 확인하는 함수이다. 다음과 같은 prototype을 가진다. first와 last는 자료구조의 범위를 나타내기 위한 iterator이며, pred는 조건을 확인하여 bool을 반환하는 함수이다. Lambda를 이용해 구현하도록 하자. 

```cpp
bool all_of (InputIterator first, InputIterator last, UnaryPredicate pred);
```

예제는 다음과 같으며 sequence는 물론이고 associative 자료 구조와도 잘 동작하는 것을 아래 예를 통해 알 수 있다. 즉, iterator를 통해 접근할 수 있는 자료 구조와 모두 잘 동작한다. 단지, map의 경우 pair를 통해 element를 받아야 한다는 것을 명심하자. 

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
    std::map<int, int> m{ {100,3}, {200,5}, {300, 7}, {101, 11},{102, 13} };
    std::unordered_set<int> us{3,5,7,11,13,17,19,23};
    std::map<int, int> um{ {100,3}, {200,5}, {300, 7}, {101, 11},{102, 13} };
    
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

#### any_of: 적어도 하나의 element라도 조건을 만족하는지 확인하는 함수

any_of 함수는 적어도 하나의 element라도 어떤 조건을 만족하는지 확인하는 함수이다. 다음과 같은 prototype을 가진다. first와 last는 자료구조의 범위를 나타내기 위한 iterator이며, pred는 조건을 확인하여 bool을 반환하는 함수이다. Lambda를 이용해 구현하도록 하자. all_of와 동일하게 사용할 수 있다.

```cpp
bool any_of (InputIterator first, InputIterator last, UnaryPredicate pred);
```

예제는 다음과 같으며 sequence는 물론이고 associative 자료 구조와도 잘 동작하는 것을 아래 예를 통해 알 수 있다. 즉, iterator를 통해 접근할 수 있는 자료 구조와 모두 잘 동작한다. 단지, map의 경우 pair를 통해 element를 받아야 한다는 것을 명심하자. 

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
    std::map<int, int> m{ {100,3}, {200,4}, {300, 7}, {101, 11},{102, 13} };
    std::unordered_set<int> us{3,4,7,11,13,17,19,23};
    std::map<int, int> um{ {100,3}, {200,4}, {300, 7}, {101, 11},{102, 13} };
    
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

#### none_of: 어느 하나의 element도 조건을 만족시키지 못하는지 확인하는 함수

none_of 함수는 어느 하나의 element도 조건을 만족시키지 못하는지 확인하는 함수이다. 다음과 같은 prototype을 가진다. first와 last는 자료구조의 범위를 나타내기 위한 iterator이며, pred는 조건을 확인하여 bool을 반환하는 함수이다. Lambda를 이용해 구현하도록 하자. all_of 또는 any_of와 동일하게 사용할 수 있지만, 조금 헷갈리기도 한다. 

```cpp
bool any_of (InputIterator first, InputIterator last, UnaryPredicate pred);
```

> Note: 조건을 어느 element도 만족시키지 못해야 true를 return하는 것이다. 

예제는 다음과 같으며 sequence는 물론이고 associative 자료 구조와도 잘 동작하는 것을 아래 예를 통해 알 수 있다. 즉, iterator를 통해 접근할 수 있는 자료 구조와 모두 잘 동작한다. 단지, map의 경우 pair를 통해 element를 받아야 한다는 것을 명심하자. 

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
    std::map<int, int> m{ {100,3}, {200,5}, {300, 7}, {101, 11},{102, 13} };
    std::unordered_set<int> us{3,5,7,11,13,17,19,23};
    std::map<int, int> um{ {100,3}, {200,5}, {300, 7}, {101, 11},{102, 13} };
    
    if ( std::none_of(ar.begin(), ar.end(), [](int i){return i%2 == 0;}) )
        std::cout << "a: None of the elements is an even number.\n";
    
    if ( std::none_of(v.begin(), v.end(), [](int i){return i%2 == 0;}) )
        std::cout << "v: None of the elements is an even number.\n";
    
    if ( std::none_of(d.begin(), d.end(), [](int i){return i%2 == 0;}) )
        std::cout << "d: None of the elements is an even number.\n";
    
    if ( std::none_of(f.begin(), f.end(), [](int i){return i%2 == 0;}) )
        std::cout << "f: None of the elements is an even number.\n";
    
    if ( std::none_of(l.begin(), l.end(), [](int i){return i%2 == 0;}) )
        std::cout << "l: None of the elements is an even number.\n";
    
    if ( std::none_of(s.begin(), s.end(), [](int i){return i%2 == 0;}) )
        std::cout << "s: None of the elements is an even number.\n";
    
    if ( std::none_of(m.begin(), m.end(), [](std::pair<int, int> p){return p.second%2 == 0;}) )
        std::cout << "m: None of the elements is an even number.\n";
    
    if ( std::none_of(us.begin(), us.end(), [](int i){return i%2 == 0;}) )
        std::cout << "us: None of the elements is an even number.\n";
    
    if ( std::none_of(um.begin(), um.end(), [](std::pair<int, int> p){return p.second%2 == 0;}) )
        std::cout << "um: None of the elements is an even number.\n";
    
    return 0;
}
```

각 자료구조의 입력에 '4'가 포함되어 있으므로 위 코드의 출력은 다음과 같다.

```
a: None of the elements is an even number.
v: None of the elements is an even number.
d: None of the elements is an even number.
f: None of the elements is an even number.
l: None of the elements is an even number.
s: None of the elements is an even number.
m: None of the elements is an even number.
us: None of the elements is an even number.
um: None of the elements is an even number.
```
