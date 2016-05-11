---
layout: post
title: leetcode-Merge Two Sorted Lists
description: leetcode
modified: 2016-05-08
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

### Problem

Handling linked lists

### Solution 

#### Approach

Linked lists problems tend to use recursion. To design a recursive function for this problem, take the following into consideration.

- What item must be returned (= What the caller function expects): the address of the head of each step must be returned.
- Start/Stop condition: Minimum requirements to run the function. In this case, at least two lists mustn't null. If both of them is null, return null. If either of them is null, return none-null list.

#### Complexity

O(n): traversal of two lists

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
#include <vector>

using namespace std;

class Solution {
public:
    int maxProfit(vector<int>& prices);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <algorithm>

int Solution::maxProfit(vector<int>& prices) {
        if(prices.size() == 0) {
            return 0;
        }
        int min_item = prices.at(0);
        int max_profit = 0;
        for (int i = 1; i < prices.size(); i++) {
            if(min_item > prices.at(i)) {
                min_item = prices.at(i);
            } else {
                max_profit = std::max(prices.at(i) - min_item, max_profit);
            }
        }
        return max_profit;
    }
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Simple Max Profit1", "[Solution]") {
    Solution so;
    vector<int> prices = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    SECTION("Max profit should be 9(0->9)") {
        REQUIRE(so.maxProfit(prices) == 9);
    }
}

TEST_CASE("Simple Max Profit2", "[Solution]") {
    Solution so;
    vector<int> prices = {9, 8, 7, 6, 5, 4, 3, 2, 0, 1};
    SECTION("Max profit should be 1(0->1)") {
        REQUIRE(so.maxProfit(prices) == 1);
    }
}
```

- To build and run

```bash
g++ -std=c++11 -c Solution.cpp -o Solution.o
g++ -std=c++11 -c SolutionTest.cpp -o SolutionTest.o
g++ -std=c++11 -o Solution Solution.o SolutionTest.o -lm
./Solution
```

#### Java

Junit used as a unit-test framework

- Solution.java

```java
package com.maxprofit;

public class Solution {
    public int maxProfit(final int[] prices) {
    	if(prices.length == 0) {
    		return 0;
    	}
    	int maximumProfit = 0;
    	int minItem = prices[0];
    	for(final int item : prices) {
    		if(item < minItem) {
    			minItem = item;
    		} else {
    			final int profit = item - minItem;
    			if(maximumProfit < profit){
    				maximumProfit = profit;
    			}
    		}
    	}
    	return maximumProfit;
    }
}
```

- SolutionTest.java

```java
package com.maxprofit.test;

import static org.junit.Assert.*;

import org.junit.Before;
import org.junit.Test;

import com.maxprofit.Solution;

public class SolutionTest {

	private Solution solution;

	@Before
	public void setUp() throws Exception {
		solution = new Solution();
	}

	@Test
	public void testMaxProfit1() {
		final int[] prices = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
		assertEquals(solution.maxProfit(prices), 9);
	}

	@Test
	public void testMaxProfit2() {
		final int[] prices = {9, 8, 7, 6, 5, 4, 3, 2, 0, 1};
		assertEquals(solution.maxProfit(prices), 1);
	}

}
```

#### Python

- Solution.py

```python
class ListNode(object):

    def __init__(self, x):
        self.val = x
        self.next = None


class Solution(object):

    def mergetwolists(self, first_sorted_list, second_sorted_list):
        if first_sorted_list is None and second_sorted_list is None:
            return None
        if first_sorted_list is None:
            return second_sorted_list
        elif second_sorted_list is None:
            return first_sorted_list
        if first_sorted_list.val < second_sorted_list.val:
            first_sorted_list.next = self.mergetwolists(
                first_sorted_list.next, second_sorted_list)
            return first_sorted_list
        else:
            second_sorted_list.next = self.mergetwolists(
                first_sorted_list, second_sorted_list.next)
            return second_sorted_list

```
