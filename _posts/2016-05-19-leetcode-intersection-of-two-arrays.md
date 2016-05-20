---
layout: post
title: leetcode-Intersection of Two Arrays
description: leetcode
modified: 2016-05-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/)

### Problem

Puzzle. The problem requires O(n) time complexity.

### Solution 

#### Approach

Hashset

#### Complexity

- Time complexity: O(n) - Hashset

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> intersection(std::vector<int>& nums1, std::vector<int>& nums2);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <algorithm>
#include <unordered_set>

std::vector<int> Solution::intersection(std::vector<int>& nums1, std::vector<int>& nums2){
	std::unordered_set<int> set;
	for(auto i: nums1){
		set.insert(i);
	}
	std::vector<int> ret = {};
	for(auto i: nums2){
		if(set.find(i) != set.end()){
			ret.push_back(i);	
			set.erase(i); //to avoid duplication
		}
	}
	return ret;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Intersection of Two Arrays - normal", "[Solution]"){
    Solution so;
    SECTION("One value"){
        std::vector<int> num1 = {1};
        std::vector<int> num2 = {1};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 1);
        REQUIRE(ret[0] == 1);
    }
    SECTION("One value"){
        std::vector<int> num1 = {1};
        std::vector<int> num2 = {1, 2};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 1);
        REQUIRE(ret[0] == 1);
    }
    SECTION("Duplicated values - 2 must be returned"){
        std::vector<int> num1 = {2, 2};
        std::vector<int> num2 = {2, 2};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 1);
        REQUIRE(ret[0] == 2);
    }
    SECTION("Duplicated values - 2 must be returned"){
        std::vector<int> num1 = {2, 2};
        std::vector<int> num2 = {2, 2, 3};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 1);
        REQUIRE(ret[0] == 2);
    }
    SECTION("Duplicated values - 2, 3 must be returned"){
        std::vector<int> num1 = {2, 2, 3, 3};
        std::vector<int> num2 = {0, 2, 2, 3, 3, 4, 5};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 2);
        REQUIRE(ret[1] == 3);
        REQUIRE(ret[2] == 2);
    }
};

TEST_CASE("Intersection of Two Arrays- boundary", "[Solution]"){
    Solution so;
    SECTION("no duplicated values"){
        std::vector<int> num1 = {1, 2, 3};
        std::vector<int> num2 = {5};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 0);
    }
    SECTION("empty input"){
        std::vector<int> num1 = {};
        std::vector<int> num2 = {1};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 0);
    }
    SECTION("empty inputs"){
        std::vector<int> num1 = {};
        std::vector<int> num2 = {};
        auto ret = so.intersection(num1, num2); 
        REQUIRE(ret.size() == 0);
    }
};

```
