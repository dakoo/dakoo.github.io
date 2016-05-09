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
package com.powerofthree;

public class Solution {
    public boolean isPowerOfThree(int n) {
    	if(n <= 0) return false;
    	while(n % 3 == 0){
    		n /= 3;
    	}
    	return n == 1;
    }
}
```

- SolutionTest.java

```java
package com.powerofthree.test;

import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;
import com.powerofthree.Solution;

public class SolutionTest {
	private Solution so;
	@Before
	public void setUp() throws Exception {
		so = new Solution();
	}

	@Test
	public void test1IsPowerOfThree() {
		assertEquals(so.isPowerOfThree(1), true);
	}
	@Test
	public void test3IsPowerOfThree() {
		assertEquals(so.isPowerOfThree(3), true);
	}
	@Test
	public void test9IsPowerOfThree() {
		assertEquals(so.isPowerOfThree(9), true);
	}
	@Test
	public void test27IsPowerOfThree() {
		assertEquals(so.isPowerOfThree(27), true);
	}
	@Test
	public void testNegative1IsNotPowerOfThree() {
		assertEquals(so.isPowerOfThree(-1), false);
	}
	@Test
	public void test0IsNotPowerOfThree() {
		assertEquals(so.isPowerOfThree(0), false);
	}
	@Test
	public void test6IsNotPowerOfThree() {
		assertEquals(so.isPowerOfThree(6), false);
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
