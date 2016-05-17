---
layout: post
title: leetcode-remove element
description: leetcode
modified: 2016-05-16
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - remove element](https://leetcode.com/problems/remove-element/)

### Problem

array index manipulation

### Solution 

#### Approach

ith index. If i is the same as the length of array, stop.
if an item on the index is the same as the target value, increase the i by 1. Otherwise copy an ith to j and then increase i and j by 1. 

An item on jth index is destination. After swapping ith item to j, it increases by 1. When the comparison stops, return the j as the length.

#### Complexity

Time complexity: O(N) 
Space complexity: O(1)

#### Cpp

- Solution.cpp

```cpp
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        auto i = 0;        
        auto j = 0; 
        auto len = nums.size();
        while(i < len){
            if(nums[i] != val){
                nums[j++] = nums[i]; 
            }
            i++;
        }
        return j;
    }
};
```
