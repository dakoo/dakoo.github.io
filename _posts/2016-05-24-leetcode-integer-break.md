---
layout: post
title: leetcode-Integer Break
description: leetcode
modified: 2016-05-25
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Integer Break](https://leetcode.com/problems/integer-break/)

### Problem

Dynamic Programming

### Solution 

#### Approach

Dynamic Programming as the following pseudo code.

```python
MP[]
loop i = 1 -> n:
	loop j = 0 -> i - 1:
		MP[i] = max(MP[i], max(MP[j]*(i-j), j*(i-j)))
return MP[n]
```

#### Complexity

Time complexity: O(n*n)

#### Cpp

- Solution.hpp

```cpp
class Solution {
public:
	int integerBreak(int n);
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>
#include <algorithm>

int Solution::integerBreak(int n) {
	std::vector<int> T(n + 1, 1);
	for (int i = 2; i <= n; i++) {
		for (int j = 1; j < i; j++) {
			T[i] = std::max(T[i], std::max(T[j] * (i - j), j*( i - j)));
		}
	}
	return T[n];
}

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Integer break - normal", "[Solution]") {
    Solution so;
    SECTION("Max product of integers consists of 2 should be 1") {
        int input = 2;
        int expected_result = 1;
        REQUIRE(so.integerBreak(input) == expected_result);
    }
    SECTION("Max product of integers consists of 3 should be 2") {
        int input = 3;
        int expected_result = 2;
        REQUIRE(so.integerBreak(input) == expected_result);
    }
    SECTION("Max product of integers consists of 4 should be 4") {
        int input = 4;
        int expected_result = 4;
        REQUIRE(so.integerBreak(input) == expected_result);
    }
    SECTION("Max product of integers consists of 6 should be 9") {
        int input = 6;
        int expected_result = 9;
        REQUIRE(so.integerBreak(input) == expected_result);
    }
    SECTION("Max product of integers consists of 10 should be 36") {
        int input = 10;
        int expected_result = 36;
        REQUIRE(so.integerBreak(input) == expected_result);
    }
};

```
