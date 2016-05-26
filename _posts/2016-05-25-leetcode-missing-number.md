---
layout: post
title: leetcode-Missing Number
description: leetcode
modified: 2016-05-24
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Missing Number](https://leetcode.com/problems/missing-number/)

### Problem

Sorting the array takes O(nlogn) time complexity. Also we should use extra space such as Hash.

### Solution 

#### Approach

Consider the property of the numbers in the array. Serial numbers with the only one missing number. The total sum of 0 to n is (0 + 1 + 2... + n - 2 + n - 1 + n) = n * (n + 1) / 2. For example, 6 for [0, 1, 2, 3] and 10 for [0, 1, 2, 3, 4].
Thus, you can get the missing number by subtracting the total sum of the array from n * ( n + 1) /2. 

- Note that the size of input array is the same as the value of the last element of the array because one element is missing.

#### Complexity

Time complexity: O(n) to get the total sum of all elements of the array.

#### Cpp

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
	int missingNumber(std::vector<int>& nums);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>
#include <iostream>

int Solution::missingNumber(std::vector<int>& nums) {
	int n = nums.size(); 
	if (n == 0)
		return -1;
	int sum = 0;
	for (auto i : nums) {
		sum += i;
	}
	return (n * (n + 1) / 2 ) - sum;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Missing Number - normal", "[Solution]") {
    Solution so;
    SECTION("Normal values 1") {
        std::vector<int> input{0, 1, 3};
        int expected_result = 2;
        REQUIRE(so.missingNumber(input) == expected_result);
    }
    SECTION("Normal values 2") {
        std::vector<int> input{0, 10, 8, 6, 4, 2, 3, 5, 7, 9};
        int expected_result = 1;
        REQUIRE(so.missingNumber(input) == expected_result);
    }
};

TEST_CASE("Missing Number - boundary case", "[Solution]") {
    Solution so;
    SECTION("Empty array should return -1") {
        std::vector<int> input{};
        int expected_result = -1;
        REQUIRE(so.missingNumber(input) == expected_result);
    }
    SECTION("[1] should return 0") {
        std::vector<int> input{1};
        int expected_result = 0;
        REQUIRE(so.missingNumber(input) == expected_result);
    }
}

```
