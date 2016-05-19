---
layout: post
title: leetcode-Single Number
description: leetcode
modified: 2016-05-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Single Number](https://leetcode.com/problems/single-number/)

### Problem

Fullfilling the requirement of O(n) time complexity is easy. However, achieving that wwithout using extra memory is difficult.

### Solution 

#### Approach

1. A Hashmap allows O(n) time complexity to meet the required time complexity. 
2. Without a hashmap, you can move forward and backward continuously to check if the item is the only one. O(n**2)
3. For O(n) without any extra space, use "XOR" bit operation. XOR properties are as follows:

- a xor b = b xor a 
- a xor (b xor c) = (a xor b) xor c 
- x xor x = 0

An algorithm with XOR is below:

```python
def findSingleNumber(llist):
	result = 0
	for i in llist:
		result = result^i
	return result

```

The algorithm is based on an idea below:

> A xor B xor C xor A xor B = (A xor A) xor (B xor B) xor C = 0 xor 0 xor C = C

#### Complexity

- Time complexity: O(n) - Iterate the input
- Space comlexity: O(1) - No extra data structure

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
    int singleNumber(std::vector<int>& nums);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"

int Solution::singleNumber(std::vector<int>& nums){
	int ret = 0;
	for( auto i : nums){
		ret ^= i;
	}
	return ret;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Find single number - Normal", "[Solution]"){
    Solution so;
    SECTION("normal 1"){
        std::vector<int> input = {1, 1, 2};
        REQUIRE(so.singleNumber(input) == 2);
    }
    SECTION("normal 2"){
        std::vector<int> input = {1, 2, 3, 1, 2};
        REQUIRE(so.singleNumber(input) == 3);
    }
};

TEST_CASE("Find single number- boundary", "[Solution]"){
    Solution so;
    SECTION("no duplicated numbers"){
        std::vector<int> input = {1};
        REQUIRE(so.singleNumber(input) == 1);
    }
    SECTION("Negative numbers"){
        std::vector<int> input = {-10, -1, -20, -10, -20};
        REQUIRE(so.singleNumber(input) == -1);
    }
    SECTION("Empty list - return 0"){
        std::vector<int> input = {};
        REQUIRE(so.singleNumber(input) == 0);
    }
};

```
