---
layout: post
title: leetcode-happy number
description: leetcode
modified: 2016-05-08
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - happy number](https://leetcode.com/problems/happy-number/)

### Problem

A puzzle handling numbers.

### Solution 

#### Approach

I used Hashset for this problem.

Store a number into hashset on each step. If the number is already stored, it means there is a cycle. 

#### Complexity

O(N)

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
class Solution {
private:
	int sumofsquareofdigits(int n);
public:
    bool isHappy(int n);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <unordered_set>
#include <iostream>

using namespace std;

int Solution::sumofsquareofdigits(int n){
	int sum = 0;
    while(n){
    	int temp = n%10;
    	sum += temp * temp;
    	n /= 10;
    }
    return sum;
}
bool Solution::isHappy(int n) {
	unordered_set<int> T;
	while(T.find(n) == T.end()){
		T.insert(n);
		if(n == 1) 
			return true;
		n = sumofsquareofdigits(n);
	}
	return false;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Happy number", "[Solution]") {
    Solution so;
    SECTION("1 should be a happy number") {
        REQUIRE(so.isHappy(1));
    }
    SECTION("19 should be a happy number") {
        REQUIRE(so.isHappy(19));
    }
}

TEST_CASE("Not power of three", "[Solution]") {
    Solution so;
    SECTION("2 shouldn't be a happy number") {
        REQUIRE(so.isHappy(2) == false);
    }
    SECTION("4 shouldn't be a happy number") {
        REQUIRE(so.isHappy(4) == false);
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
package com.ishappy;
import java.util.*;

public class Solution {
	private int sumofsquareofdigits(final int inputData){
		int sum = 0;
		int temporaryData = inputData;
		while(temporaryData > 0){
			final int remainder = temporaryData % 10;
			sum += remainder*remainder;
			temporaryData /= 10;
		}
		return sum;
	}
    public boolean isHappy(final int inputData) {
    	Set<Integer> hashset = new HashSet<Integer>();
		int temporaryData = inputData;
    	while(!hashset.contains(temporaryData)){
    		hashset.add(temporaryData);
    		if(temporaryData == 1){
    			return true;
    		}
    		temporaryData = sumofsquareofdigits(temporaryData);
    	}
    	return false;
    }
}
```

- SolutionTest.java

```java
package com.ishappy.test;

import static org.junit.Assert.*;

import org.junit.Before;
import org.junit.Test;

import com.ishappy.Solution;

public class SolutionTest {

	private Solution so;

	@Before
	public void setUp() throws Exception {
		so = new Solution();
	}

	@Test
	public void testIsHappy_1() {
		assertEquals(so.isHappy(1), true);
	}

	@Test
	public void testIsHappy_19() {
		assertEquals(so.isHappy(19), true);
	}

	@Test
	public void test_Not_IsHappy_2() {
		assertEquals(so.isHappy(2), false);
	}

	@Test
	public void test_Not_IsHappy_4() {
		assertEquals(so.isHappy(4), false);
	}
}
```

#### Python

unittest used as a unittest framework.

- Solution.py

```python
class Solution(object):

    def _sumofsquareofdigits(self, n):
        sum_ = 0
        while n > 0:
            temp = int(n % 10)
            sum_ = sum_ + (temp * temp)
            n = n / 10
        return sum_

    def ishappy(self, n):
        items = set()
        while n not in items:
            items.add(n)
            if n == 1:
                return True
            n = self._sumofsquareofdigits(n)
        return False
```

- SolutionTest.py

```python
import unittest

from Solution import Solution


class TestUM(unittest.TestCase):

    def setUp(self):
        self.so = Solution()

    def test_happynumber_1(self):
        self.assertEqual(self.so.ishappy(1), True)

    def test_happynumber_19(self):
        self.assertEqual(self.so.ishappy(19), True)

    def test_not_happynumber_2(self):
        self.assertEqual(self.so.ishappy(2), False)

    def test_not_happynumber_4(self):
        self.assertEqual(self.so.ishappy(4), False)

if __name__ == "__main__":
    unittest.main()
```
