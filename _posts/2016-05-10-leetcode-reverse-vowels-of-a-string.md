---
layout: post
title: leetcode-Reverse Vowels of a String
description: leetcode
modified: 2016-05-10
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Reverse Vowels of a String](https://leetcode.com/problems/reverse-vowels-of-a-string/)

### Problem

String handling using loop manipulation

### Solution 

#### Approach

- Manupulate 2 pointers, which are moving from front and back.
- Check if a character is a vowel, use hashset. 

#### Complexity

O(N): Thanks to hashset

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
package com.reverseVowels;
import java.util.*;

public class Solution {
    public String reverseVowels(String s) {
    	if(s == null){
    		return null;
    	}
    	HashSet<Character> hashset = new HashSet<Character>();
    	char[] vowels = {'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'};
    	for(char c : vowels){
    		hashset.add(c);
    	}
    	char[] charArray = s.toCharArray();
    	int length = s.length();
    	int forwardIndex = 0;
    	int backwardIndex = length - 1;
    	while(forwardIndex <= backwardIndex) {
    		while(forwardIndex< length && !hashset.contains(charArray[forwardIndex])) {
    			forwardIndex++;
    		}
    		while(backwardIndex >= 0 && !hashset.contains(charArray[backwardIndex])) {
    			backwardIndex--;
    		}
    		if( forwardIndex >= length || backwardIndex < 0 || forwardIndex >= backwardIndex) {
    			break;
    		}
    		char temp = charArray[forwardIndex];
    		charArray[forwardIndex] = charArray[backwardIndex];
    		charArray[backwardIndex] = temp;
    		forwardIndex++;
    		backwardIndex--;
    	}
    	return new String(charArray);
    }
}
```

- SolutionTest.java

```java
package com.reverseVowels.test;

import static org.junit.Assert.*;

import org.junit.Before;
import org.junit.Test;

import com.reverseVowels.Solution;

public class SolutionTest {

	Solution so;
	
	@Before
	public void setUp() throws Exception {
		so = new Solution();
	}

	@Test
	public void testReverseVowelsNormal() {
		assertEquals("Holle", so.reverseVowels("Hello"));
	}

	@Test
	public void testReverseVowelsNoVowels() {
		assertEquals("BCD", so.reverseVowels("BCD"));
	}

	@Test
	public void testReverseVowelsEmptyString() {
		assertEquals(null, so.reverseVowels(null));
	}

	@Test
	public void testReverseVowelsAllVowels() {
		assertEquals(so.reverseVowels("aeioue"), "euoiea");
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
