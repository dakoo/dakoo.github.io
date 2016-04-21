---
layout: post
title: leetcode-move zeroes
description: leetcode
modified: 2016-04-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - move zeros](https://leetcode.com/problems/move-zeroes/)

### Problem

The key change of this is doing this in-place without a copy of the array.

### Solution

#### Approach

Look. Think of quick sort. Based on a pivot value, the smaller ones move to the front side and the bigger ones move to the back side. This problem is very similar to quick sort.None-0s move to the front side and 0s move to the back-side. 

##### Quick sort

The following is the C++ code of quick sort.

```cpp
int partition(vector<int> &T, int low, int high){
	int index = low;
	int pivot = T[high];
	for(int i = low; i < high; i++){
		if(T[i] < pivot){
			swap(&T[i], &T[index]
			index++;
		}
	}
	swap(&T[high], &T[index]);
	return index;
}
```

We can adjust the quic sort flow for the moveZeros as follows.

1. Pivot value is 0. Pivot index has no meaning. : Iteration must be the first to last item. We don't need to swap the pivot and index.  
2. if the value of an index is not zero, swap two items i and index

#### Cpp

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
	void moveZeroes(vector<int> &T){
		int len = static_cast<int>(T.size());
		int index = 0;
		int pivot = 0;
		for(int i = 0; i < len ; i++){
			if(T[i] != 0){
				int tmp = T[index];
				T[index] = T[i];
				T[i] = tmp;	
				index++;
			}		
		} 
	}
};
int main(){
	vector<vector<int> > T = {
					{},
					{0, 1, 0, 0, 2, 0},
					{0, 0, 0, 0, 1, 2},
					{1, 2, 0, 0, 0, 0},
					{1, 0, 0, 0, 0, 2},
					{1, 0, 0, 0, 0, 2},
					{0, 0, 0, 0, 0, 0},
					{0, -1, 1, 2, -2, 0},
					{1, 1, 1, 1, 2, 2},};

	Solution s;
	for(auto t : T){
		for(auto i : t){
			cout << i << " ";
		}
		cout << endl;
		s.moveZeroes(t);
		for(auto i : t){
			cout << i << " ";
		}
		cout << endl<< "---------------------"<< endl;
	}
	return 0;
}
```

#### Java

```java
import java.util.*;

public class Solution {
	public void moveZeroes(int[] nums){
		int index = 0;
		for(int i = 0; i<nums.length; i++){
			if(nums[i] != 0){
				int tmp = nums[i];
				nums[i] = nums[index];
				nums[index] = tmp;
				index++;
			}
		}
	}
	public static void main(String[] argc){
		int[][] T = {
					{},
					{0, 1, 0, 0, 2, 0},
					{0, 0, 0, 0, 1, 2},
					{1, 2, 0, 0, 0, 0},
					{1, 0, 0, 0, 0, 2},
					{1, 0, 0, 0, 0, 2},
					{0, 0, 0, 0, 0, 0},
					{0, -1, 1, 2, -2, 0},
					{1, 1, 1, 1, 2, 2},};		
		Solution s = new Solution();
		for(int[] t : T){
			for(int i : t){
				System.out.print(i);	
			}
			System.out.println();
			s.moveZeroes(t);
			for(int i : t){
				System.out.print(i);	
			}
			System.out.println();
			System.out.println("--------------------------");
		}
	}
}
```

#### Python

Python implemenation is dramatically short!!!

```python
class Solution(object):
    def moveZeroes(self, nums):
    	index = 0;
    	for i in range(len(nums)):
    		if nums[i] != 0:
    			nums[index], nums[i] = nums[i], nums[index] 
    			index += 1

if __name__ == "__main__":
	s = Solution();	
	T = [
		[],
		[0, 1, 0, 0, 2, 0],
		[0, 0, 0, 0, 1, 2],
		[1, 2, 0, 0, 0, 0],
		[1, 0, 0, 0, 0, 2],
		[1, 0, 0, 0, 0, 2],
		[0, 0, 0, 0, 0, 0],
		[0, -1, 1, 2, -2, 0],
		[1, 1, 1, 1, 2, 2],];	
	for t in T:
		print(t)
		s.moveZeroes(t);
		print(t)
		print("-------------------------------")
```
