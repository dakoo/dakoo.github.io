---
layout: post
title: leetcode-contains duplicate
description: leetcode
modified: 2016-04-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - contains duplicate](https://leetcode.com/problems/contains-duplicate/)

### Problem

Finding out if duplicate items is in inputs. 

### Solution 

#### Approach

	Let's use HashSet for C++ and java. Since the implementation in each language is slightly different, the flows are different. 
	For python, we can take an simpler appoach that compare the length of original list and the length of the set with the list. 

#### Complexity

The complexity is O(N) because hashmap has O(1) for lookup and O(1) for insert.

#### Cpp

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>

using namespace std;

class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
    	unordered_set<int> T; 
    	for(auto i : nums){
    		auto it = T.find(i);
    		if(it!= T.end()) return true;
    		T.insert(i);
    	}
    	return false;
    }
};

int main(){
	vector<vector<int> > testcase = {
		{ 1, 2, 3, 1},
		{ 1, 2, 3, 1, 2, 3},
		{ 1, 2, 3, 4},
		{ 1, 2, 3, 4, 5, 6},};
	Solution so;
	for(auto t : testcase){
		if(so.containsDuplicate(t))
			cout << "Duplicated" << endl;
		else 
			cout << "Not duplicated" << endl;
	}
	return 0;
}
```

#### Java

```java

import java.util.*;

public class Solution {
    public boolean containsDuplicate(int[] nums) {
  		HashSet<Integer> T = new HashSet<Integer>();        
  		for(int i : nums){
  			if(T.add(i) == false)
  				return true;
  		}
  		return false;
    }
    public static void main(String[] argc){
    	int[][] testcases = {
    		{ 1, 2, 3, 1},
    		{ 1, 2, 3, 1, 2, 3},
    		{ 1, 2, 3, 4},
    		{ 1, 2, 3, 4, 5, 6},};
  		Solution so = new Solution();
  		for(int[] t : testcases){
  			if(so.containsDuplicate(t))
  				System.out.println("Duplicated");
  			else 
  				System.out.println("Not duplicated");
  		}	
    }
}
```

#### Python

- containsDuplicate1: Using set 
- containsDuplicate2: Simpler implementation using comparision of length

```python
class Solution(object):
    def containsDuplicate1(self, nums):
    	T = set()
    	for i in nums:
    		if i in T : return True
    		T.add(i)
    	return False
    	
    def containsDuplicate2(self, nums):
    	return len(nums) != len(set(nums))

if __name__ == "__main__" :
	testcases = [ 
		[ 1, 2, 3, 1],
		[ 1, 2, 3, 1, 2, 3],
		[ 1, 2, 3, 4],
		[ 1, 2, 3, 4, 5, 6],];
	so = Solution()
	for t in testcases:
		if so.containsDuplicate1(t):
			print("Duplicated")
		else:
			print("Not duplicated")
	print("------------")		
	for t in testcases:
		if so.containsDuplicate2(t):
			print("Duplicated")
		else:
			print("Not duplicated")
```
