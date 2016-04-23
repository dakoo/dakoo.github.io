---
layout: post
title: leetcode-reverse string
description: leetcode
modified: 2016-04-22
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - reverse string](https://leetcode.com/problems/reverse-string/)

### Problem

This problem asks if you know how to iterate loop and string. 

### Solution

#### Approach

Simply, iterate a string in a reverse way with the mechanisms of each language.
	
#### Cpp

```cpp
#include <iostream>
#include <string>

using namespace std;

class Solution {
public:
	string reverseString(string s){
		string ns;
		for(auto rit = s.rbegin(); rit != s.rend(); rit++){
			ns.push_back(*rit);
		}
		return ns;
	}
};

int main(){
	string str[4] = { "h", "hello", "h o", "" };
	Solution s;
	for(auto t : str){
		cout << s.reverseString(t) << endl;
	}
	return 0;
}
```

#### Java

```java
public class Solution {
    public String reverseString(String s) {
    	return (new StringBuffer(s)).reverse().toString();
  	} 
  	public static void main(String[] argc){
  		String[] str = { "h", "hello", "h o", "" };
  		Solution so = new Solution();
  		for(String s : str){
  			System.out.println(so.reverseString(s));
  		}
  	}
}
```

#### Python

```python
class Solution(object):
    def reverseString(self, s):
    	return s[::-1]

if __name__ == "__main__" :
	testcase = ["h", "hello", "h o", ""]
	so = Solution()
	for s in testcase:
		print(so.reverseString(s))
```
