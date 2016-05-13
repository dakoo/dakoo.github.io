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

##### C and Java

- Manupulate 2 pointers, which are moving from front and back.
- Check if a character is a vowel, use hashset. 

##### python

- Regular expression should be considered to manipulate String in python

#### Complexity

O(N): Thanks to hashset

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
#include <string>

using namespace std;

class Solution {
public:
    string reverseVowels(string s);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <unordered_set>
#include <vector>

string Solution::reverseVowels(string str) {
	if(str == ""){
		return "";
	}	
	vector<char> vowels = {'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'};
	unordered_set<char> hashset;
	for(auto c: vowels){
		hashset.insert(c);
	}
	int len = str.size();
	int front_index = 0;
	int back_index = len - 1;
	while(front_index < back_index){
		while(front_index < len && hashset.find(str[front_index])==hashset.end()){
			front_index++;
		}
		while(back_index >= 0 && hashset.find(str[back_index])==hashset.end()){
			back_index--;
		}
		if(front_index >= back_index){
			break;
		}			
		char temp = str[front_index];
		str[front_index] = str[back_index];
		str[back_index] = temp;
		front_index++;
		back_index--;
	}
	return str;
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Reverse Vowels - Normal", "[Solution]") {
    Solution so;
    SECTION("hello should become holle") {
        REQUIRE(so.reverseVowels("hello") == "holle");
    }
    SECTION("HELLO should become HOLLE") {
        REQUIRE(so.reverseVowels("HELLO") == "HOLLE");
    }
}

TEST_CASE("Reverse Vowels - exceptional", "[Solution]") {
    Solution so;
    SECTION("BCD should become BCD") {
        REQUIRE(so.reverseVowels("BCD") ==  "BCD");
    }
    SECTION("null should become null") {
        REQUIRE(so.reverseVowels("") == "");
    }
    SECTION("aeioue should become euoiea") {
        REQUIRE(so.reverseVowels("aeioue") == "euoiea");
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

- Python regular expression : refer to [here](http://devanix.tistory.com/296)

- Solution.py

```python
import re


class Solution(object):

    def reverse_vowels(self, str):
        if str is None:
            return None
        vowels = re.findall("[aeiouAEIOU]", str)
        return re.sub("[aeiouAEIOU]", lambda any: vowels.pop(), str)

```

- SolutionTest.py

```python
import unittest

from Solution import Solution


class TestUM(unittest.TestCase):

    def setUp(self):
        self.so = Solution()

    def test_reverse_vowels_normal(self):
        self.assertEqual(self.so.reverse_vowels("Holle"), "Hello")

    def test_reverse_vowels_no_vowel(self):
        self.assertEqual(self.so.reverse_vowels("BCD"), "BCD")

    def test_reverse_vowels_empty_string(self):
        self.assertEqual(self.so.reverse_vowels(None), None)

    def test_reverse_vowels_all_vowels(self):
        self.assertEqual(self.so.reverse_vowels("aeioue"), "euoiea")

if __name__ == "__main__":
    unittest.main()

```
