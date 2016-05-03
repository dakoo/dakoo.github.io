---
layout: post
title: leetcode-two sum
description: leetcode
modified: 2016-05-01
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - two sum](https://leetcode.com/problems/two-sum/)

### Problem

Puzzle with data structure.
In addition, be familiar with using hashmap and array of each language.

### Solution 

#### Approach

To minimize the number of access to the data, let's use a hash table.
You can use set instead of hashmap unless the problem is to get a pair of indices instead of values.

1. Insert all items into hashtable. 
2. Traverse the items. During the traversal, examine if the counter-part item exists.

#### Complexity

O(n): Hash table

#### Cpp

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>

using namespace std;

class Solution{
public:
	vector<int> twoSum(vector<int>& nums, int target){
		unordered_map<int, int> T;
		vector<int> res;
		for(int i = 0; i< nums.size(); i++){
			T[nums[i]] = i;
		}
		for(int i = 0; i< nums.size(); i++){
			auto it = T.find(target - nums[i]);
			if(it != T.end() && it->second != i){
				res.push_back(i);
				res.push_back(it->second);
				break;
			}
		}
		return res;
	}
};

int main(){
	Solution so;
	vector<int> testcase = {3, 2, 4};
	int target = 6;
	vector<int> result = so.twoSum(testcase, target);
	cout << result[0] << " " << result[1] << endl;
	return 0;
}
```

#### Java

```java
import java.util.*;

public class Solution {
    public int[] twoSum(int[] nums, int target) {
    	HashMap<Integer, Integer> T = new HashMap<Integer, Integer>();
		for(int i = 0; i < nums.length; i++){
			T.put(nums[i], i);
		}	    	 
    	int[] res = new int[2];
		for(int i = 0; i < nums.length; i++){
			if(T.containsKey(target - nums[i])){
				int index = T.get(target-nums[i]);
				if(index == i) continue;
				res[0] = i;
				res[1] = index;
				break;
			}
		}	    	 
		return res;
    }
    public static void main(String[] argc){
    	Solution so = new Solution();
    	int[] testcase = {3, 2, 4};
    	int target = 6;
    	int[] res = so.twoSum(testcase, target);
		System.out.println(res[0] + " " + res[1]);    	
    }
}
```

#### Python

```python
class Solution(object):
	def twoSum(self, nums, target):
		T = dict()
		for i in range(len(nums)):
			T[nums[i]] = i	
		for i in range(len(nums)):
			index = T.get(target-nums[i], -1) 
			if index != -1 and index != i:
				return [i, index]
if __name__ == "__main__":
	so = Solution()
	print(so.twoSum([3, 2, 4], 6)) #1 2
```
