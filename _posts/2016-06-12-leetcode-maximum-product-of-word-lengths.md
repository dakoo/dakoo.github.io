---
layout: post
title: leetcode-Maximum Product of Word Lengths
description: leetcode
modified: 2016-06-12
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Maximum Product of Word Lengths](https://leetcode.com/problems/maximum-product-of-word-lengths/)

### Problem

Handling string and loops. Puzzle!

### Solution 

Two problems must be resolved.

1. Two words don't have any common letters.
2. Find the maximum product

#### Approach


##### Two words don't have any common letters

1. Each letter of a word can be mapped a bit like this. So 26 alphabets can be mapped 26 bits.

Note that ord() in python would get the int value of the char

```python
def makebits(word):
    ret = 0
    for c in word:
        bit = ord(c) - ord('a') //ascii operation
        ret = ret | 1 << bit
    return ret
```

2. Compare two words in bits

```python
def hasCommonLetter(first_word_bits, second_word_bits):
    if (first_word_bits & second_word_bits) != 0:
        return True
    return False
```

##### Find the maximum product

- O(n**2) algorithm to compare all pairs

```python
def findmax(words):
    bits_table = list()
    for word in words:
        bits_table.append(makebits(word))
    ret = 0
    for i in range(1, len(bits_table)):
        for j in range(0, i):
            if HasCommonLetter(bits_table[i], bits_table[j]) != True:  
                if (len(words[i]) * len(words[j])) > ret:
                    ret = len(words[i]) * len(words[j])
    return ret
```

##### Complexity

- O(N**2) : To compare all the pairs

### Implementation

#### C++

- Solution.hpp

```cpp
#include <vector>
#include <string>

class Solution {
private:
    int makebits(std::string &word);
    bool hasCommonLetter(int first_word_bits, int second_word_bits);
public:
    int maxProduct(std::vector<std::string>& words);
};

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include <string>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Maximum Product of Word Lengths - normal", "[Solution]") {
    Solution so;
    SECTION("Normal 1") {
        std::vector<std::string> input{"a", "bcd"};
        int expected_result = 3;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
    SECTION("Normal 2") {
        std::vector<std::string> input{"ab", "cd", "ef"};
        int expected_result = 4;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
    SECTION("Normal 3") {
        std::vector<std::string> input{"abcw",
            "baz", "foo", "bar", "xtfn", "abcdef"};
        int expected_result = 16;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
    SECTION("Normal 4") {
        std::vector<std::string> input{"a",
            "ab", "abc", "d", "cd", "bcd", "abcd"};
        int expected_result = 4;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
};
TEST_CASE("Maximum Product of Word Lengths - exception", "[Solution]") {
    Solution so;
    SECTION("No such pair of words -- return 0") {
        std::vector<std::string> input{"a", "aa", "aaa", "aaaa"};
        int expected_result = 0;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
    SECTION("Empty input -- return 0") {
        std::vector<std::string> input{};
        int expected_result = 0;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
    SECTION("One input -- return 0") {
        std::vector<std::string> input{"a"};
        int expected_result = 0;
        REQUIRE(so.maxProduct(input) == expected_result);
    }
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>
#include <string>
#include <algorithm>

int Solution::makebits(std::string &word) {
    int ret = 0;
    for (auto c : word) {
        int bit = c - 'a';
        ret = ret | (0x1 << bit);
    }
    return ret;
}
bool Solution::hasCommonLetter(int first_word_bits, int second_word_bits) {
    if ((first_word_bits & second_word_bits) == 0)
        return false;
    return true;
}
int Solution::maxProduct(std::vector<std::string>& words) {
    int len = words.size();
    if (len < 2)
        return 0;
    std::vector<int> table;
    for (auto word : words) {
        table.emplace_back(makebits(word));
    }
    int max_product = 0;
    for (int i = 1; i < len; i++) {
        for (int j = 0; j < i ; j++) {
            if (hasCommonLetter(table[i], table[j]))
                continue;
            int product = static_cast<int> (words[i].length() * words[j].length());
            max_product = std::max(max_product, product);
        }
    }
    return max_product;
}

```
