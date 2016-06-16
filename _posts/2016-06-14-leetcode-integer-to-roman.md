---
layout: post
title: leetcode-Integer to Roman
description: leetcode
modified: 2016-06-15
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Integer to Roman](https://leetcode.com/problems/integer-to-roman/)

### Problem

Write a function to change integer to roman numeral. The input range is from 1 to 3999. The relationship between integer and roman numberal is as follows:

- I - 1
- IV -4
- V - 5
- IX - 9
- X - 10
- XL - 40
- L - 50
- XC - 90
- C - 100
- CD - 400
- D - 500
- CM - 900
- M - 1000

### Solution 

#### Approach

Let's assume the value of input is smaller than 10 (1~9). We can consider the following relationship.

- I - 1
- IV -4
- V - 5
- IX - 9

We can store them in an array. Then find an element which is the largest one among equal to or smaller than the value using bisectional search.

For example, if the input value is 7, 5 is the largest one in 1, 4, and 5. 
Then write V that is a corresponding roman numeral to 5. Then do it again with the remaining value 2. The result is 'VII'.

For the general procedures for the algorithm is as follows:

1. Using bisectional search, find the largest one among equal to or smaller than the value.
2. Append the corresponding roman numeral to the result string
3. Repeat 1 and 2 with the remaining input value. 

##### Complexity

- Time complexity: O(n)

### Implementation

#### C++

- Solution.hpp

```cpp
#include <string>
#include <vector>

class Solution {
private:
    int findTheLargestAmongEqualToorSmallerThan(int val, std::vector<int> &IntegerTable, int low_index, int high_index);
public:
    std::string intToRoman(int num);
};

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <string>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Integer to Roman - normal", "[Solution]") {
    Solution so;
    SECTION("Integer 1 should return I") {
        int input = 1;
        std::string expected_result("I");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 7 should return VII") {
        int input = 7;
        std::string expected_result("VII");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 9 should return IX") {
        int input = 9;
        std::string expected_result("IX");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 13 should return XIII") {
        int input = 13;
        std::string expected_result("XIII");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 49 should return XLIX") {
        int input = 49;
        std::string expected_result("XLIX");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 98 should return XCVIII") {
        int input = 98;
        std::string expected_result("XCVIII");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 583 should return DLXXXIII") {
        int input = 583;
        std::string expected_result("DLXXXIII");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 810 should return DCCCX") {
        int input = 810;
        std::string expected_result("DCCCX");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 1111 should return MCXI") {
        int input = 1111;
        std::string expected_result("MCXI");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
    SECTION("Integer 3999 should return MMMCMXCIX") {
        int input = 3999;
        std::string expected_result("MMMCMXCIX");
        REQUIRE(so.intToRoman(input) == expected_result);
    }
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <string>
#include <vector>

int Solution::findTheLargestAmongEqualToorSmallerThan(int val,
    std::vector<int> &IntegerTable, int low_index, int high_index) {
    int low = low_index;
    int high = high_index;
    int answer = 0;
    while (low <= high) {
        int mid = (low + high) / 2;
        int e_val = IntegerTable.at(mid);
        if (e_val <= val) {
            low = mid + 1;
            answer = mid;
        } else {
            high = mid - 1;
        }
    }
    return answer;
}

std::string Solution::intToRoman(int num) {
    std::vector<int> IntegerTable = {1,
        4, 5, 9, 10, 40, 50, 90, 100, 400, 500, 900, 1000};
    std::vector<std::string> RomanNumeralTable = {"I",
        "IV", "V", "IX", "X", "XL", "L", "XC", "C", "CD", "D", "CM", "M"};
    int val = num;
    int high = IntegerTable.size() - 1;
    std::string ret;
    while (val > 0) {
        int index = findTheLargestAmongEqualToorSmallerThan(val,
            IntegerTable, 0, high);
        ret = ret + RomanNumeralTable.at(index);
        val = val - IntegerTable.at(index);
        high = index;
    }
    return ret;
}

```
