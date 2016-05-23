---
layout: post
title: leetcode-Best Time to Buy and Sell Stock II
description: leetcode
modified: 2016-05-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

### Problem

Puzzle.

### Solution 

#### Approach

Don't focus on a pair of buying and selling. To resolve this problem,

1. Ignore not increasing values. You don't have to take them into consideration. 
2. Sum all the increasing gaps. 

For example, let's take a look at the following inputs, 

'''
Input : 1 2 3 2 1 2 2 3 4 

- 1->2 : +1
- 2->3 : +1
- 3->2 : ignore
- 2->1 : ignore
- 1->2 : +1
- 2->2 : ignore
- 2->3 : +1
- 3->4 : +1
'''

#### Complexity

Time complexity: O(N)

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
    int maxProfit(std::vector<int>& prices);
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

int Solution::maxProfit(std::vector<int>& prices) {
	if (prices.size() <= 1)
		return 0;
	int profit = 0;
	for (int i = 1; i < prices.size(); i++) {
		int gap = prices[i] - prices[i - 1];
		if (gap > 0)
			profit += gap;
	}
	return profit;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Best Time to Buy and Sell Stock - normal", "[Solution]") {
    Solution so;
    SECTION("Normal values 1") {
        std::vector<int> input{1, 2};
        REQUIRE(so.maxProfit(input) == 1);
    }
    SECTION("Normal values 2") {
        std::vector<int> input{1, 2, 0, 2};
        REQUIRE(so.maxProfit(input) == 3);
    }
    SECTION("Normal values 3") {
        std::vector<int> input{1, 2, 2};
        REQUIRE(so.maxProfit(input) == 1);
    }
    SECTION("Normal values 4") {
        std::vector<int> input{2, 1, 2};
        REQUIRE(so.maxProfit(input) == 1);
    }
    SECTION("Normal values 5") {
        std::vector<int> input{1, 2, 3};
        REQUIRE(so.maxProfit(input) == 2);
    }
    SECTION("Normal values 6 - comprehensive") {
        std::vector<int> input{3, 1, 3, 2, 2, 3, 2, 1, 1, 2, 3, 2, 1, 2, 3, 1};
        REQUIRE(so.maxProfit(input) == 7);
    }
};

TEST_CASE("Best Time to Buy and Sell Stock - boundary cases", "[Solution]") {
    Solution so;
    SECTION("An empty input should return 0") {
        std::vector<int> input{};
        REQUIRE(so.maxProfit(input) == 0);
    }
    SECTION("One day input should return 0") {
        std::vector<int> input{1};
        REQUIRE(so.maxProfit(input) == 0);
    }
    SECTION("input with non-increasing values should return 0") {
        std::vector<int> input{2, 1};
        REQUIRE(so.maxProfit(input) == 0);
    }
    SECTION("input with the same values should return 0") {
        std::vector<int> input{2, 2, 2};
        REQUIRE(so.maxProfit(input) == 0);
    }
}

```
