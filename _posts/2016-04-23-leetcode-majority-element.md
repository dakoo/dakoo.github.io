---
layout: post
title: leetcode-Majority Element
description: leetcode
modified: 2016-04-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - majority element](https://leetcode.com/problems/majority-element/)

### Problem

Finding the majority problem with some constraints. Because of the constraints you can resolve this with various way. 

### Solution 

#### Approach

I handle this as a general counting problem. Count all items using hashmap.

#### Cpp

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>

using namespace std;

class Solution {
public:
	int majorityElement(vector<int>& nums){
		unordered_map<int, int> Counts;
		int total = (int)nums.size();
		int ret = 0;
		for( auto i : nums){
			if(++Counts[i] >= total/2.0){
				ret = i;
				break;
			}
		}
		return ret; 
	}
};

int main(){
	vector<vector<int> > T = { 
		{ 1 },
		{ 1, 0 }, 
		{ 0, 1, 1 },
		{ 1, 1, 0, 0},
		{ 0, 0, 1, 1, 1},
		{ 1, 1, 1, 0, 0},
		{ 1, 0, 0, 0, 1, 1, 1}};
	Solution s;
	for(auto t : T){
		cout << s.majorityElement(t);
	}
	return 0;
}
```

#### Java

```java
import java.util.*;

public class Solution {
    public int majorityElement(int[] nums) {
    	HashMap<Integer, Integer> Counts = new HashMap<Integer, Integer>(); 
    	int ret = 0;
    	for(int i : nums){
    		Integer num = Counts.get(i);
    		if(num == null) 	
    			Counts.put(i, num = 1);
    		else 
    			Counts.put(i, ++num);
			if(num >= nums.length/2.0) {
				ret = i;
				break;
			}
    	}
    	return ret;
    }
    public static void main(String[] argc){
    	int[][] T = {
		{ 1 },
		{ 1, 0 }, 
		{ 0, 1, 1 },
		{ 1, 1, 0, 0},
		{ 0, 0, 1, 1, 1},
		{ 1, 1, 1, 0, 0},
		{ 1, 0, 0, 0, 1, 1, 1}};
		Solution so =  new Solution();
		for(int[] t : T){
			System.out.println(so.majorityElement(t));
		}
    }
}
```

#### Python

```python
class Solution(object):
    def majorityElement(self, nums):
    	T = {}	
    	for i in nums:
    		num = T.get(i, 0) + 1
    		if num >= len(nums)/2.0:
    			return i	
    		T[i] = num

if __name__ == "__main__" :
	testcase = [ 
		[ 1 ],
		[ 1, 0 ], 
		[ 0, 1, 1 ],
		[ 1, 1, 0, 0],
		[ 0, 0, 1, 1, 1],
		[ 1, 1, 1, 0, 0],
		[ 1, 0, 0, 0, 1, 1, 1],]
	s = Solution()
	for t in testcase:
		print(s.majorityElement(t))
```
