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


