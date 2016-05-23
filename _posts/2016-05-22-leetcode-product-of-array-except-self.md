---
layout: post
title: leetcode-Product of Array Except Self
description: leetcode
modified: 2016-05-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

### Problem

Puzzle.
Easiest approach: Product all items and divide the result by each number. However, we shouldn't use division. Thus we shoud find a different approach.

### Solution 

#### Approach

Iterate from left to right and then from right to left as below.


1) Step 1 : from left to right. The value of the first time is 1. Then product the previous result and the value of the previous item.



```
Left[]
0   1  2  3                  4               5,          6            : index
1,                   ,nums[0]*..*nums[3],         ,nums[0]*..*nums[5] : value
```

2) Step 2 : from right to left. The value of the first time is 1. Then product the previous result and the value of the previous item.

```
Right[]
0                    1  2  3     4                   5  6            : index
nums[1]*...*nums[6],         , nums[5]*nums[6] ,        1            : value
```

3) Step3 :

```
Result[]
Result[i] = Left[i] * Right[i]
```

You can opimize the algorithm above to cut the Left[] and Right[].

#### Complexity

Time complexity: O(N)

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> productExceptSelf(std::vector<int>& nums);
};
```

- Solution.cpp with several arrays (Easy to understand)

```cpp
#include "Solution.hpp"
#include <iostream>

std::vector<int> Solution::productExceptSelf(std::vector<int>& nums) {
	std::vector<int> result(nums.size());
	if (nums.size() == 0)
		return result;
	if (nums.size() == 1) {
		result[0] = nums[0];
		return result;
	}
	std::vector<int> left(nums.size(), 1);
	std::vector<int> right(nums.size(), 1);
	for (int i = 1; i < nums.size(); i++) {
		left[i] = left[i - 1] * nums[i - 1];
	}
	for (int i = nums.size() - 2; i >= 0; i--) {
		right[i] = right[i + 1] * nums[i + 1];
	}
	for (int i = 0; i < nums.size(); i++) {
		result[i] = left[i] * right[i];
	}
	return result;
}

```

- Solution.cpp with O(1) space complexity

```cpp
#include "Solution.hpp"
#include <iostream>

std::vector<int> Solution::productExceptSelf(std::vector<int>& nums) {
	std::vector<int> result(nums.size(), 1);
	if (nums.size() == 0)
		return result;
	if (nums.size() == 1) {
		result[0] = nums[0];
		return result;
	}
	for (int i = 1; i < nums.size(); i++) {
		result[i] = result[i - 1] * nums[i - 1];
	}
	int product_right_items = 1;
	for (int i = nums.size() - 2; i >= 0; i--) {
		product_right_items = product_right_items * nums[i + 1];
		result[i] = product_right_items * result[i];
	}
	return result;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Product of Array Except Self - normal", "[Solution]") {
    Solution so;
    SECTION("4 values") {
        std::vector<int> input{1, 2, 3, 4};
        std::vector<int> expected{24, 12, 8, 6};
        auto result = so.productExceptSelf(input);
        auto index = 0;
        REQUIRE(result.size() == 4);
        for (auto i : result) {
            REQUIRE(i == expected[index++]);
        }
    }
};

TEST_CASE("Product of Array Except Self - boundary cases", "[Solution]") {
    Solution so;
    SECTION("empty input") {
        std::vector<int> input{};
        auto result = so.productExceptSelf(input);
        REQUIRE(result.size() == 0);
    }
    SECTION("one input") {
        std::vector<int> input{4};
        std::vector<int> expected{4};
        auto result = so.productExceptSelf(input);
        auto index = 0;
        REQUIRE(result.size() == 1);
        for (auto i : result) {
            REQUIRE(i == expected[index++]);
        }
    }
}

```
