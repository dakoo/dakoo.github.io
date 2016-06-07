---
layout: post
title: leetcode-Longest Increasing Subsequence
description: leetcode
modified: 2016-06-05
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

### Problem

Implement Longest Increase Subsequence O(n**2) and O(nlogn) algorithms.


### Solution 

##### O(n**2) Algorithm

Refer to my article about [easy LIS algorithm](http://hochulshin.com/Longest-Increasing-Subsequence/)

```cpp
function(A[],n){
  T[]
  for(int i = 1; i<n; i++){
    T[i] = 1 
    for(int j = i - 1; j< i; j++){
      T[i] = max(T[i], T[j] + In[i]);
    }
  }
	return max value of T[]
}
```

##### O(nlogn) Algorithm 

Refer to my article about [efficient LIS algorithm](http://hochulshin.com/algorithm-longest-increasing-subsequence-nlogn/)

### Cpp

##### O(N**2) Algorithm

- Solution.cpp

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if(nums.size() == 0)
            return 0;
        vector<int> T(nums.size(), 1);
        int ret = 1;
        for(int i = 1; i< nums.size(); i++){
            for(int j = 0; j < i; j++){
                if(nums[i] > nums[j]){
                    T[i] = max(T[i], T[j]+1);
                    ret = max(ret, T[i]);
                }
            }
        }
        return ret;
    }
};
```


##### O(N**2) Algorithm

- Solution.hpp

```cpp
#include <vector>

class Solution {
    int findIndexMinOfSameOrBigger(std::vector<int>& nums, int& targetVal);
public:
    int lengthOfLIS(std::vector<int>& nums);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

int Solution::findIndexMinOfSameOrBigger(std::vector<int>& nums, int& targetVal) {
    int answerIndex = -1;
    int lowerBoundIndex = 0;
    int upperBoundIndex = nums.size() - 1;
    while (lowerBoundIndex <= upperBoundIndex) {
        int middleIndex = (lowerBoundIndex + upperBoundIndex) / 2;
        if (targetVal <= nums.at(middleIndex)) {
            answerIndex = middleIndex;
            upperBoundIndex = middleIndex - 1;
        } else {
            lowerBoundIndex = middleIndex + 1;
        }
    }
    return answerIndex;
}
int Solution::lengthOfLIS(std::vector<int>& nums) {
    if(nums.size() == 0)
        return 0;
    std::vector<int> LIS;
    for (auto i : nums) {
        auto len = LIS.size();
        if (len == 0) {
            LIS.emplace_back(i);
        } else if (LIS.at(0) > i) {
            LIS[0] = i;
        } else if (i > LIS.at(len - 1)) {
            LIS.emplace_back(i);
        } else {
            int placeIndex = findIndexMinOfSameOrBigger(LIS, i);
            LIS[placeIndex] = i;
        }
    }
    return LIS.size();
}
```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("Longest Increasing Subsequence - normal", "[Solution]") {
    Solution so;
    SECTION("Normal 1") {
        std::vector<int> input{1, 2};
        int expected_result = 2;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
    SECTION("Normal 2") {
        std::vector<int> input{2, 1};
        int expected_result = 1;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
    SECTION("Normal 3") {
        std::vector<int> input{2, 1, 2, 3};
        int expected_result = 3;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
    SECTION("Normal 4") {
        std::vector<int> input{2, 1, 2, 1, 2, 1, 3, 1, 2, 3, 4};
        int expected_result = 4;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
    SECTION("Normal 5") {
        std::vector<int> input{10, 9, 2, 5, 3, 7, 101, 18};
        int expected_result = 4;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
};
TEST_CASE("Longest Increasing Subsequence - exception", "[Solution]") {
    Solution so;
    SECTION("Empty array should return 0") {
        std::vector<int> input;
        int expected_result = 0;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
    SECTION("No increasing sequence should return 1") {
        std::vector<int> input{5, 4, 3, 2, 1};
        int expected_result = 1;
        REQUIRE(so.lengthOfLIS(input) == expected_result);
    }
};
```
