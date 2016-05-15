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
package com.uglynumber;

public class Solution {
    public boolean isUgly(int num) {
    	if(num <= 0) return false;
    	int[] T = {2, 3, 5};
    	for(int t : T){
    		while(num % t == 0){
    			num = num / t;
    		}
    	}
    	return num == 1;
    }
}
```

- SolutionTest.java

```java
package com.uglynumber.test;

import static org.junit.Assert.*;
import com.uglynumber.Solution;

import org.junit.Test;
import org.junit.Before;

public class SolutionTest {

	private Solution so;

    @Before
    public void setUp() throws Exception {
    	so = new Solution();
    }
	
	@Test
	public void test_ugly_values() {
		int[] T= {6, 8};
		for(int t : T){
			 assertEquals(so.isUgly(t), true);
		}
	}

	@Test
	public void test_none_ugly_values() {
		int[] T= {14};
		for(int t : T){
			 assertEquals(so.isUgly(t), false);
		}
	}

	@Test
	public void test_boundary_ugly_values() {
		int[] T= {1};
		for(int t : T){
			 assertEquals(so.isUgly(t), true);
		}
	}

	@Test
	public void test_boundary_none_ugly_values() {
		int[] T= {-1, 0};
		for(int t : T){
			 assertEquals(so.isUgly(t), false);
		}
	}
}
```

#### Python

unittest used as a unittest framework.

- Solution.py

```python
class Solution(object):
    def isUgly(self, num):
        if num <= 0: return False
        for t in [2, 3, 5]:
            while num % t == 0:
                num = num / t
        return num == 1
```

- SolutionTest.py

```python
import unittest
from Solution import Solution

class TestUM(unittest.TestCase): 
    def setUp(self):
        self.so = Solution()

    def test_ugly_values(self):
        for t in [6, 8]:
            self.assertEqual( self.so.isUgly(t), True)

    def test_none_ugly_values(self):
        for t in [14]:
            self.assertEqual( self.so.isUgly(t), False)

    def test_boundary_ugly_values(self):
        for t in [1]:
            self.assertEqual( self.so.isUgly(t), True)

    def test_boundary_none_ugly_values(self):
        for t in [-1, 0]:
            self.assertEqual( self.so.isUgly(t), False)

if __name__ == "__main__":
    unittest.main()
```
