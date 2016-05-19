---
layout: post
title: leetcode-Counting Bits
description: leetcode
modified: 2016-05-15
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Counting Bits](https://leetcode.com/problems/counting-bits/)

### Problem

Puzzle. 
Time complexity O(n) and Space complexity O(n) required. Space complexity is to store a result.

### Solution 

#### Approach

Dynamic programming

- The LSB of each number is 0 or 1.
- If you rightshift the number by 1 bit, the result may be calculated already before. 
- Then if the LSB of the original number is 1, increase the number of '1' by 1. 

Here is the pseudo code: 

```python
def cal(num):
	if num == 0:
		return [0]
	results=[0, 1] # for i = 0, 1 
	for i in range(2, num+1):
		temp = results[i >> 1] + (i & 1)
		results.append(temp)
	return results

```

#### Complexity

- Time complexity: O(n) - DP
- Space comlexity: O(n) - list to restore a result

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

- Solution.hpp

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> countBits(int num);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"

std::vector<int> Solution::countBits(int num) {
	std::vector<int> ret = {0};
	if(num == 0)
		return ret;
	ret.push_back(1);
	for(auto i = 2 ; i< num + 1 ; i++){
		ret.push_back(ret[i >> 1] + (i & 0x1));
	}
	return ret;
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Counting Bits - Normal", "[Solution]"){
    Solution so;
    SECTION("2"){
        auto res = so.countBits(2); 
        std::vector<int> expected_result = {0, 1, 1};
        REQUIRE(res.size() == expected_result.size());
        int index = 0;
        for( auto i : expected_result){
            REQUIRE(i == res[index++]);
        }
    }
    SECTION("8"){
        auto res = so.countBits(8); 
        std::vector<int> expected_result = {0, 1, 1, 2, 1, 2, 2, 3, 1};
        REQUIRE(res.size() == expected_result.size());
        int index = 0;
        for( auto i : expected_result){
            REQUIRE(i == res[index++]);
        }
    }
};

TEST_CASE("Counting Bits - boundary", "[Solution]"){
    Solution so;
    SECTION("0"){
        auto res = so.countBits(0); 
        std::vector<int> expected_result = {0};
        REQUIRE(res.size() == expected_result.size());
        REQUIRE(res[0] == expected_result[0]);
    }
    SECTION("1"){
        auto res = so.countBits(1); 
        std::vector<int> expected_result = {0,1};
        REQUIRE(res.size() == expected_result.size());
        REQUIRE(res[0] == expected_result[0]);
        REQUIRE(res[1] == expected_result[1]);
    }
};
```

- To build and run

```bash
g++ -std=c++11 -c Solution.cpp -o Solution.o
g++ -std=c++11 -c SolutionTest.cpp -o SolutionTest.o
g++ -std=c++11 -o Solution Solution.o SolutionTest.o -lm
./Solution
```
