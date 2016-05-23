---
layout: post
title: Intersection of Two Arrays II
description: leetcode
modified: 2016-05-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/)

### Problem

An a-number-of-items problem.

### Solution 

#### Approach

Hashmap is a good tool for solving a-number-of-items problems. The algorithm is as follows:

1. Constuct a hashmap with an array. The count of each item is stored on the map. 
2. With the other array, if an item's count is larger than 0, add the item to the vector and reduce the count of the item on the map.

#### Complexity

Time complexity: O(N)

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
	std::vector<int> intersect(std::vector<int>& nums1, std::vector<int>& nums2);
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>
#include <unordered_map>

std::vector<int> Solution::intersect(std::vector<int>& nums1, std::vector<int>& nums2) {
	std::vector<int> result;
	if (nums1.size() == 0 || nums2.size() == 0)
		return result;
	std::unordered_map<int, int> map;
	for (auto i : nums1) {
		map[i]++;
	}
	for (auto i : nums2) {
		auto it = map.find(i);
		if (it != map.end() && it->second > 0) {
			result.emplace_back(i);
			map[i]--;
		}
	}
	return result;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Intersection of Two Arrays II - normal", "[Solution]") {
    Solution so;
    SECTION("Normal values 1") {
        std::vector<int> input1{1, 2, 2, 1};
        std::vector<int> input2{2, 2};
        std::vector<int> expected{2, 2};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
    SECTION("Normal values 2") {
        std::vector<int> input1{1, 2, 2, 2, 2, 3};
        std::vector<int> input2{2, 2};
        std::vector<int> expected{2, 2};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
    SECTION("Normal values 3") {
        std::vector<int> input1{1, 2, 3, 3, 1, 2};
        std::vector<int> input2{2, 2};
        std::vector<int> expected{2, 2};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
    SECTION("Normal values 4") {
        std::vector<int> input1{2, 2};
        std::vector<int> input2{1, 2, 3, 3, 1, 2};
        std::vector<int> expected{2, 2};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
};

TEST_CASE("Intersection of Two Arrays II - boundary cases", "[Solution]") {
    Solution so;
    SECTION("Empty array should return empty array 1") {
        std::vector<int> input1{};
        std::vector<int> input2{};
        std::vector<int> expected{};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
    }
    SECTION("Empty array should return empty array 2") {
        std::vector<int> input1{1, 2, 3, 4};
        std::vector<int> input2{};
        std::vector<int> expected{};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
    }
    SECTION("Empty array should return empty array 3") {
        std::vector<int> input1{};
        std::vector<int> input2{1, 2, 3, 4};
        std::vector<int> expected{};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
    }
    SECTION("None intersection arrays should return empty array") {
        std::vector<int> input1{5, 6, 7};
        std::vector<int> input2{1, 2, 3, 4};
        std::vector<int> expected{};
        auto result = so.intersect(input1, input2);
        REQUIRE(result.size() == expected.size());
    }
}

```
