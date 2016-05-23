---
layout: post
title: leetcode-Top K Frequent Elements
description: leetcode
modified: 2016-05-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

### Problem

Kth most frequent elements within O(NlogN) time complexity

### Solution 

#### Approach

Use max-heap. After construct max-heap, in where the key of elements is the frequency, pop the elements k times. 

1. Construct hashmap to store the key and frequency.
2. Convert the hashmap into a heap. 
3. Pop the items from the heap k times and push them to a vector.

#### Complexity

Time complexity: O(N)

#### Cpp

- Hashmap: `map[i]++` is enough to push items into Hashmap. You don't have to check if the item is there or not.  
- Prioriy Queue: Refer to the following code to understand how you can deal with pair<int, int> with priority queue in C++.

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> topKFrequent(std::vector<int>& nums, int k);
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>
#include <queue>
#include <unordered_map>

class Compare {
public:
	bool operator()(std::pair<int, int> item1, std::pair<int, int> item2) {
		return item1.second < item2.second; //for max heap
	};
};

std::vector<int> Solution::topKFrequent(std::vector<int>& nums, int k) {
	/*hashmap*/
	std::unordered_map<int, int> map;
	for (auto i : nums) {
		map[i]++;
	}
	/*priority queue*/
	std::priority_queue<std::pair<int, int>, std::vector<std::pair<int, int> >, Compare> heap;
	for (auto p : map) {
		heap.push(std::pair<int, int>(p.first, p.second));
	}
	/*vector*/
	std::vector<int> ret;
	for (int i = 0; i < k; i++) {
		auto item = heap.top();
		heap.pop();
		ret.push_back(item.first);
	}
	return ret;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Top K Frequent Elements - normal", "[Solution]") {
    Solution so;
    SECTION("Normal values 1") {
        std::vector<int> input{1, 1, 1, 2, 2, 3};
        int k = 2;
        std::vector<int> expected{1, 2};
        auto result = so.topKFrequent(input, k);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
    SECTION("Normal values 2") {
        std::vector<int> input{1, 2, 2, 3, 1, 1, 1, 2};
        int k = 2;
        std::vector<int> expected{1, 2};
        auto result = so.topKFrequent(input, k);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
};

TEST_CASE("Top K Frequent Elements- boundary cases", "[Solution]") {
    Solution so;
    SECTION("Empty array should return empty array") {
        std::vector<int> input{};
        int k = 0;
        std::vector<int> expected{};
        auto result = so.topKFrequent(input, k);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
    SECTION("One element array should return an array with the element") {
        std::vector<int> input{1};
        int k = 1;
        std::vector<int> expected{1};
        auto result = so.topKFrequent(input, k);
        REQUIRE(result.size() == expected.size());
        for (int i = 0; i < result.size(); i++) {
            REQUIRE(result.at(i) == expected.at(i));
        }
    }
}

```
