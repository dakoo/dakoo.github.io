---
layout: post
title: leetcode-power of two
description: leetcode
modified: 2016-04-26
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - power of two](https://leetcode.com/problems/power-of-two/)

### Problem

Handling numbers. Check if a give number is a power of 2, or not. 

### Solution 

#### Approach

Check the following 3 cases with a give number, n.

1. If n is 0, it's not a power of 2.  
2. If n is smaller than 0, it's not a power of 2.  
3. If n&(n-1) == 0, it is a power of 2 because a power of 2 has only one '1'.

Keep in mind, n & (n>>1) is not the same as n & (n-1)

#### Complexity

O(1)

#### Cpp

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution{
public:
	bool isPowerOfTwo(int n){
		return (n > 0) && !(n & (n-1));
	}
};
int main(){
	vector<int> testcases = { 1, 2, 4, 5,  8, 16 }; 
	Solution so;
	for(auto i : testcases){
		if(so.isPowerOfTwo(i))
			cout << "Power of Two" << endl; //1, 2, 4, 8, 16
		else 
			cout << "Not Power of Two" << endl; //5
	}
	return 0;
}
```

#### Java

```java
public class Solution {
    public boolean isPowerOfTwo(int n) {
    	return (n > 0 && (n & (n - 1)) == 0);
    }
    public static void main(String[] argc){
    	int[] testcases= {1, 2, 4, 5, 8, 16};
    	Solution so = new Solution();
    	for(int i : testcases){
    		if(so.isPowerOfTwo(i) == true)
    			System.out.println("Power of 2"); //1, 2, 4, 8, 16
    		else 
    			System.out.println("Not Power of 2"); //5
    	}
    }
}
```

#### Python

```python
class Solution(object):
    def isPowerOfTwo(self, n):
    	return n > 0 and n & (n - 1) == 0

if __name__ == "__main__" :
	testcases = [ 1, 2, 4, 5, 8, 16];
	so = Solution()
	for i in testcases:
		if so.isPowerOfTwo(i) == True:
			print("Power of Two") 		# 1 , 2, 4, 8, 16
		else:
			print("Not Power of Two") 	# 5
```
