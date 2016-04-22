---
layout: post
title: leetcode-valid anagram
description: leetcode
modified: 2016-04-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - valid anagram](https://leetcode.com/problems/valid-anagram/)

### Problem

If you solve this problem as lowercase alphabet rearannge problem as described, it's too easy. Assume almost all kinds of characters.  

### Solution

#### Approach

Check how many times each character is used in each string. If the number is the same, it's anagram. Hashtable is the best data structure for this.  

#### Cpp

```cpp
#include <unordered_map>
#include <iostream>
#include <string>
#include <algorithm>

using namespace std;
class Solution {
public:
    bool isAnagram(string s, string t) {
		unordered_map<char, int> T;	        
		for(auto c : s) {
			auto it = T.find(c);
			if(it != T.end())
				T[c] = it->second + 1;
			else
				T[c] = 1;
		}
		for(auto c : t) {
			auto it = T.find(c);
			if(it != T.end()) {
				if(it->second > 1)
					T[c] = it->second - 1;
				else if (it->second == 1)
					T.erase(c);	
				else 
					return false;
			}
			else
				return false;
		}
		if(T.size() != 0)
			return false;
		return true;
    }
};


int main(){
	Solution so;
	string s[4] = {"hello", "", "", "h l"};
	string t[4] = {"hello", "", "o", "hel"};

	for(int i = 0; i<4; i++){
		if(so.isAnagram(s[i], t[i]))
			cout << s[i] << " and " << t[i] << " is anagram" << endl;
		else 
			cout << s[i] << " and " << t[i] << " is NOT anagram" << endl;
	}

	return 0;
}
```

#### Java

```java
-
```

#### Python

```python
-
```
