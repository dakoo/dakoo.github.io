---
layout: post
title: leetcode-power of three
description: leetcode
modified: 2016-05-05
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - power of three](https://leetcode.com/problems/power-of-three/)

### Problem

A puzzle handling numbers.

### Solution 

#### Approach

I used iteration for this problem.

1. Divide a number by 3 until the remainder is not divided by 3.
2. If the remainder is 1(3/3 == 1), the given number is a power of three.

#### Complexity

O(N)

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
class Solution {
public:
    bool isPowerOfThree(int n);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"

bool Solution::isPowerOfThree(int n){
	if(n <= 0) return false;
	while(n % 3 == 0){
		n /= 3;
	}
	return n == 1;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE( "Power of three", "[Solution]" ){
	Solution so;
	SECTION("1 should be power of three"){
		REQUIRE(so.isPowerOfThree(1));
	}
	SECTION("3 should be power of three"){
		REQUIRE(so.isPowerOfThree(3));
	}
	SECTION("9 should be power of three"){
		REQUIRE(so.isPowerOfThree(9));
	}
	SECTION("27 should be power of three"){
		REQUIRE(so.isPowerOfThree(27));
	}
}

TEST_CASE( "Not power of three", "[Solution]"  ){
	Solution so;
	SECTION("-1 shouldn't be power of three"){
		REQUIRE_FALSE(so.isPowerOfThree(-1));
	}
	SECTION("0 shouldn't be power of three"){
		REQUIRE_FALSE(so.isPowerOfThree(0));
	}
	SECTION("6 shouldn't be power of three"){
		REQUIRE_FALSE(so.isPowerOfThree(6));
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
    def isPowerOfThree(self, n):
    	if n <= 0: return False
    	while n % 3 == 0: 
    		n = n / 3
    	return n == 1
```

- SolutionTest.py

```python
import unittest
from Solution import Solution

class TestUM(unittest.TestCase): 
    def setUp(self):
        self.so = Solution()

    def test_power_of_three_1(self):
        self.assertEqual(self.so.isPowerOfThree(1), True)

    def test_power_of_three_3(self):
        self.assertEqual(self.so.isPowerOfThree(3), True)

    def test_power_of_three_9(self):
        self.assertEqual(self.so.isPowerOfThree(9), True)

    def test_power_of_three_27(self):
        self.assertEqual(self.so.isPowerOfThree(27), True)

    def test_not_power_of_three_negative1(self):
        self.assertEqual(self.so.isPowerOfThree(-1), False)

    def test_not_power_of_three_0(self):
        self.assertEqual(self.so.isPowerOfThree(0), False)

    def test_not_power_of_three_6(self):
        self.assertEqual(self.so.isPowerOfThree(6), False)
        
if __name__ == "__main__":
    unittest.main()
```
