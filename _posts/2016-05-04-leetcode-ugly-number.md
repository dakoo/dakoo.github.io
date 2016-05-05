---
layout: post
title: leetcode-ugly number
description: leetcode
modified: 2016-05-04
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - ugly number](https://leetcode.com/problems/ugly-number/)

### Problem

A puzzle handling numbers.

### Solution 


Don't be captivated by the word 'prime number'. It's not a problem to determine if the numbers are prime numbers. 
Instead, it's a problem to determine if a given number is divided by 2, 3 and 5. 

#### Approach

Let's take steps below. Note that 2/2 = 1, 3/3 = 1, and 5/5 = 1.

1. Divide a number by 2 until the remainder is not divided by 2.
2. Divide a number by 2 until the remainder is not divided by 3.
3. Divide a number by 2 until the remainder is not divided by 5.
4. The remainder is 0 or 1, the given number is an ugly number.

#### Complexity

O(1)

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
class Solution {
public:
    bool isUgly(int num);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

using namespace std;

bool Solution::isUgly(int num) {
	if(num <= 0) return false;
	vector<int> T = {2, 3, 5};
	for(auto t : T){
		while(num % t == 0){
			num = num / t;	
		}
	}
	return num == 1;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE( "Ugly values", "[Solution]" ){
	Solution so;
	SECTION("6 should be ugly"){
		REQUIRE(so.isUgly(6));
	}
	SECTION("8 should be ugly"){
		REQUIRE(so.isUgly(8));
	}
}

TEST_CASE( "None-ugly values", "[Solution]"  ){
	Solution so;
	SECTION("14 should be not ugly"){
		REQUIRE_FALSE(so.isUgly(14));
	}
}

TEST_CASE( "Boundary values: [0, negative numbers, 1]", "[Solution]" ){
	Solution so;
	SECTION("0 should be not ugly"){
		REQUIRE_FALSE(so.isUgly(0));
	}
	SECTION("-1 should be not ugly"){
		REQUIRE_FALSE(so.isUgly(-1));
	}
	SECTION("1 should be ugly"){
		REQUIRE(so.isUgly(1));
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
