---
layout: post
title: leetcode-Best Time to Buy and Sell Stock   
description: leetcode
modified: 2016-05-08
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### Problem

Handling arrays

### Solution 

#### Approach

Traverse an array with the following procedure.

- If an item is smaller than smallest one, update the smallest one.
- Otherwise, if the profit (the item - the smallest one) is bigger than the maximum profit, update the maximum profit.

#### Complexity

O(n): traversal of an array

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

unittest used as a unittest framework.

- Solution.py

```python
class Solution(object):
    def maxprofit(self, prices):
        min_item = prices[0]
        max_profit = 0
        for item in prices:
            if item < min_item:
                min_item = item
            else:
                max_profit = max([max_profit, (item - min_item)])
        return max_profit

```

- SolutionTest.py

```python
import unittest
from Solution import Solution


class TestUM(unittest.TestCase):

    def setUp(self):
        self.so = Solution()

    def test_max_profit1(self):
        self.assertEqual(self.so.maxprofit([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]), 9)

    def test_max_profit2(self):
        self.assertEqual(self.so.maxprofit([9, 8, 7, 6, 5, 4, 3, 2, 0, 1]), 1)

if __name__ == '__main__':
    unittest.main()

```
