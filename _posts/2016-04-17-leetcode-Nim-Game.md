---
layout: post
title: leetcode-Nim-Game
description: leetcode
modified: 2016-04-17
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - Nim Game](https://leetcode.com/problems/nim-game/)

### Problem

It's a simple puzzle. No data structure and special strategy required.

### Solution

#### Approach

On the description, if there are 4 stones then you will never win. That is, this game is to make the opponent 4. To do that, try to make the opponent 4, 8, 12, 16...For example, if there are 9 stones, remove one stone to make it 8. Then, the opponent cannot make you be 4. Only you can make the opponent 4.

#### Cpp

```cpp
#include <iostream>
#include <cassert>

using namespace std;
class Solution {
public:
	bool canWinNim(int n){
		if(n <= 3) 
			return true; //always win
		if(n % 4 == 0)
			return false;
		return true;
	}
};

/* test with border cases */
int main(){
	Solution s;
	assert(s.canWinNim(3));
	assert(!s.canWinNim(4));
	assert(s.canWinNim(5));
	assert(s.canWinNim(7));
	assert(!s.canWinNim(8));
	assert(!s.canWinNim(1000));
	assert(s.canWinNim(1001));
	return 0;
}
```

#### Java

```java
public class Solution {
	public boolean canWinNim(int n){
		if(n <= 3) 
			return true;		
		if( n%4  == 0)
			return false;
		return true;
	}
	public static void main(String[] args){
		Solution s = new Solution();
		if(s.canWinNim(3) != true)
			throw new IllegalStateException();
		if(s.canWinNim(4) != false)
			throw new IllegalStateException();
		if(s.canWinNim(5) != true)
			throw new IllegalStateException();
		if(s.canWinNim(7) != true)
			throw new IllegalStateException();
		if(s.canWinNim(8) != false)
			throw new IllegalStateException();
		if(s.canWinNim(1000) != false)
			throw new IllegalStateException();
		if(s.canWinNim(1001) != true)
			throw new IllegalStateException();
	}
}
```

#### Python

```python
class test(object):
	def canWinNim(self, n):
		if n <= 3:
			return True
		if n%4 == 0:
			return False
		return True

if __name__ == "__main__":
	t = test()
	if t.canWinNim(3) != True: 
		raise NameError("")
	if t.canWinNim(4) != False: 
		raise NameError("")
	if t.canWinNim(7) != True: 
		raise NameError("")
	if t.canWinNim(8) != False: 
		raise NameError("")
	if t.canWinNim(1000) != False: 
		raise NameError("")
	if t.canWinNim(1001) != True: 
		raise NameError("")
```
