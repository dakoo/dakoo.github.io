---
layout: post
title: leetcode-house robber
description: leetcode
modified: 2016-05-15
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - house robber](https://leetcode.com/problems/house-robber/)

### Problem

Dynamic programming problem

### Solution 

#### Approach

Maximum value at i_th index is bigger one between taking the nums[i_th](That is, maximum value at i-2th index + value of i_th index) and not taking the nums[i_th] (That is, maximum value at i-1th index). Simply, 

```
temp = max(curr, prev + ith)
prev = curr
curr = temp
```

#### Complexity

O(N) : To traverse the array once. 

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
#include <vector>

using namespace std;

class Solution {
public:
    int rob(vector<int>& nums); 
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <algorithm>

int Solution::rob(vector<int>& nums) {
    int count = nums.size();
    auto prev = 0;
    auto curr = 0;
    for(int i = 0 ; i < count ; i++){
        auto temp = max(prev + nums[i], curr);
        prev = curr; 
        curr = temp;
    }
    return curr;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("House Robber - Simple", "[Solution]") {
    Solution so;
    vector<int> houses1 = {0, 1, 2};
    SECTION("Max profit should be 2") {
        REQUIRE(so.rob(houses1) == 2);
    }

    vector<int> houses2 = {0, 1, 2, 4};
    SECTION("Max profit should be 5") {
        REQUIRE(so.rob(houses2) == 5);
    }

}

TEST_CASE("House Robber - Boundary case", "[Solution]") {
    Solution so;
    vector<int> houses1 = {0};
    SECTION("Max profit should be 0") {
        REQUIRE(so.rob(houses1) == 0);
    }

    vector<int> houses2 = {1, 0, 0, 1};
    SECTION("Max profit should be 2") {
        REQUIRE(so.rob(houses2) ==2);
    }
    
    vector<int> houses3 = {0, 1, 1, 1};
    SECTION("Max profit should be 2") {
        REQUIRE(so.rob(houses3) ==2);
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
package com.houserobber;

public class Solution {
    public int rob(int[] nums) {
    	if(nums == null || nums.length == 0){
    		return 0;
    	}
        int curr = 0;
        int prev = 0;
        for(int i: nums) {
            int temp = Math.max(curr, prev + i);
            prev = curr;
            curr = temp;
        }
        return curr;
    }
}
```

- SolutionTest.java

```java
package com.houserobber.test;

import static org.junit.Assert.*;

import org.junit.Test;
import org.junit.Before;

import com.houserobber.Solution;

public class SolutionTest {

	private Solution so;
	@Before
	public void setUp() throws Exception {
		so = new Solution();
	}
	@Test
	public void testHouseRobberNormal1() {
		int[] nums = {0, 1, 2};
		assertEquals(2, so.rob(nums));
	}
	@Test
	public void testHouseRobberNormal2() {
		int[] nums = {0, 1, 2, 4};
		assertEquals(so.rob(nums), 5);
	}
	@Test
	public void testHouseRobberEmpty() {
		int[] nums = {0};
		assertEquals(0, so.rob(nums));
	}
	@Test
	public void testHouseRobberSymmetric() {
		int[] nums = {1, 0, 0, 1};
		assertEquals(2, so.rob(nums));
	}
	@Test
	public void testHouseRobberAsymmetric() {
		int[] nums = {0, 1, 1, 1};
		assertEquals(2, so.rob(nums));
	}

}
```

#### Python

unittest used as a unittest framework.

- Solution.py

```python
class Solution(object):

    def rob(self, nums):
        curr, prev = 0, 0
        for i in nums:
            temp = max(curr, prev + i)
            curr, prev = temp, curr
        return curr

```

- SolutionTest.py

```python
import unittest

from Solution import Solution


class TestUM(unittest.TestCase):

    def setUp(self):
        self.so = Solution()

    def test_house_robber_normal1(self):
        self.assertEqual(self.so.rob([0, 1, 2]), 2)

    def test_house_robber_normal2(self):
        self.assertEqual(self.so.rob([0, 1, 2, 4]), 5)

    def test_house_robber_empty(self):
        self.assertEqual(self.so.rob([0]), 0)

    def test_house_robber_symmetric(self):
        self.assertEqual(self.so.rob([1, 0, 0, 1]), 2)

    def test_house_robber_asymmetric(self):
        self.assertEqual(self.so.rob([0, 1, 1, 1]), 2)

if __name__ == "__main__":
    unittest.main()

```
