---
layout: post
title: leetcode-Find the Duplicate Number
description: leetcode
modified: 2016-05-26
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

### Problem

Handling arrays.

### Solution 

#### Approach

Sort(Quick Sort) then check the duplication.

#### Complexity

Time complexity: O(nlogn)

#### Cpp

- Solution.hpp

```cpp
#include <vector>
class Solution {
public:
    int findDuplicate(std::vector<int>& nums);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>
#include <algorithm>

int Solution::findDuplicate(std::vector<int>& nums){
	if(nums.size() == 0)
		return 0;
	std::sort(nums.begin(), nums.end());
	int prev = nums.at(0);
	for(auto it = nums.begin(); it!= nums.end(); it++){
		if(it != nums.begin() && *it == prev)
			return *it;
		prev = *it;
	}
	return 0;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Find the Duplicate Number - normal", "[Solution]") {
    Solution so;
    SECTION("Normal 1") {
        std::vector<int> input{1, 2, 2};
        int expected_result = 2;
        REQUIRE(so.findDuplicate(input) == expected_result);
    }
    SECTION("Normal 2") {
        std::vector<int> input{2, 1, 1};
        int expected_result = 1;
        REQUIRE(so.findDuplicate(input) == expected_result);
    }
    SECTION("Normal 3") {
        std::vector<int> input{1, 2, 1, 1};
        int expected_result = 1;
        REQUIRE(so.findDuplicate(input) == expected_result);
    }
    SECTION("Normal 4") {
        std::vector<int> input{1, 2, 4, 1};
        int expected_result = 1;
        REQUIRE(so.findDuplicate(input) == expected_result);
    }
};
TEST_CASE("Find the Duplicate Number - exception", "[Solution]") {
    Solution so;
    SECTION("Empty array should return 0") {
        std::vector<int> input{};
        int expected_result = 0;
        REQUIRE(so.findDuplicate(input) == expected_result);
    }
};

```
