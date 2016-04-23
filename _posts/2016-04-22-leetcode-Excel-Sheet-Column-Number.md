---
layout: post
title: leetcode-Excel Sheet Column Number
description: leetcode
modified: 2016-04-22
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - Excel Sheet Column Number](https://leetcode.com/problems/excel-sheet-column-number/)

### Problem

This problem is converting 26 demcimal number to decimal number. For example, AA is 1 * 26 + 1 = 27.

### Solution

#### Approach

Make a converting function which iterate the string.
	
#### Cpp

```cpp
#include <iostream>
#include <string>

using namespace std;

class Solution {
public:
	int titleToNumber(string s){
		int sum = 0;
		for(auto c : s){
			int num = c - 'A' + 1;
			sum = sum * 26 + num;
		}
		return sum;
	}
};

int main(){
	string str[3] = { "A", "AB", "AAZ" };
	Solution s;
	for(auto t : str){
		cout << s.titleToNumber(t) << endl;
	}
	return 0;
}
```

#### Java

```java
public class Solution {
    public int titleToNumber(String s) {
    	int sum = 0;
 		  for(char c : s.toCharArray()) {
 			  int num = c - 'A' + 1;
 			  sum = sum * 26 + num; 
 		  } 
 		  return sum;
    }
    public static void main(String[] argc){
		  String str[] = { "A", "AB", "AAZ" };
    	Solution so = new Solution();
    	for(String t : str){
    		System.out.println(so.titleToNumber(t));
    	}
    }
}
```

#### Python

What a beautiful code. Let's be familiar with the reduce and lambda function in python.

```python
from functools import reduce

class Solution(object):
    def titleToNumber(self, s):
    	return reduce(lambda x, y : x * 26 + y, [ord(c) - ord('A') + 1 for c in list(s)])
    	
if __name__ == "__main__" :
	testcase = ["A", "AB", "AAZ"]
	so = Solution()
	for s in testcase:
		print(so.titleToNumber(s))
```
