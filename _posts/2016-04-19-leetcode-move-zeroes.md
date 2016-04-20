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

1. Find a zero and none-zero item. If neither of them cannot'be found, stop.
2. If index of none-zero is larger than zero, swap zero and none-zero item and increase the index.Otherwise, increase the index of none-zero. 
3. Repeat 1 and 2 unless either of indices is out of the array.

#### Cpp

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution {
public:
	void moveZeroes(vector<int> &T){
    int len = static_cast<int>(T.size());
		int i_zero = 0;
		int i_none_zero = 0;

		while(i_zero < len && i_none_zero < len) {
			while(T[i_zero] != 0){ 
				i_zero++;
				if(i_zero == len) return;
			}
			while(T[i_none_zero] == 0) {
				i_none_zero++;
				if(i_none_zero == len) return;
			}
			if(i_none_zero > i_zero){
				//swap zero and none-zero
				T[i_zero] = T[i_none_zero];
				T[i_none_zero] = 0;
				i_zero++;
				i_none_zero++;
			} else {
				i_none_zero++;
			}
		}
	}
};
int main(){
	vector<vector<int> > T = {{},
					{0, 1, 0, 0, 2, 0},
					{0, 0, 0, 0, 1, 2},
					{1, 2, 0, 0, 0, 0},
					{1, 0, 0, 0, 0, 2},
					{1, 0, 0, 0, 0, 2},
					{0, 0, 0, 0, 0, 0},
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
1
```

#### Python

```python
2
```
