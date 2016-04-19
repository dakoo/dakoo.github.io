---
layout: post
title: leetcode-add-digits
description: leetcode
modified: 2016-04-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - Add Digits](https://leetcode.com/problems/add-digits/)

### Problem

It's a simple puzzle. No data structure and special strategy required. 
I just found out the pattern to solve it. If you have some mathmatical background,you can make much faster solutions.

### Solution

#### Approach

Key problem is "Without loop or recursion".That is,  we need to solve this while moving from first digit to last digit. So, we can make a kind of incremental approach as below.

```
In case of 123456789
- incremental
1 --> 1 
2 --> 1 + 2 : 3 is less than 10
3 --> 3 + 3 : 6 is less than 10
4 --> 6 + 4 : 10 is not less than 10 --> 10's digit + 1's digit --> 1 + 0 --> 1
5 --> 1 + 5 : 6 is less than 10 
6 --> 6 + 6 : 12 --> 3
7 --> 3 + 7 : 10 --> 1
8 --> 1 + 8 : 9
9 --> 9 + 9 : 18  --> 9
--> 9

- original 
12356789 = 45 --> 9
```

You can verify the approach with a number,56893948 as below.

```
- incremental
5: 5
6: 56 -> 11 -> 2
8: 28 -> 10 -> 1
9: 19 -> 10 -> 1
3: 13 -> 4
9: 49 -> 13 -> 4
4: 44 -> 8
8: 88 -> 16 -> 7
--> 7

- original 
56893948 = 52 --> 7 
```
It works well!

#### Cpp

```cpp
#include <iostream>
#include <vector>
#include <cassert>

using namespace std;

class Solution {
public:
	int addDigits(int num){
		vector<int> vec;
		for(int value = num; value > 0; value/=10){
			vec.push_back(value%10);
		}
		int sum = 0;
		for(auto i: vec){	//accessed by value
			sum += i;
			if(sum >= 10) sum = sum/10 + sum%10;
		}
		return sum;
	}		
};

int main(){
	Solution s;
	assert(s.addDigits(123456789) == 9);
	assert(s.addDigits(56893948) == 7);
	return 0;
}
```

#### Java

```java
import java.util.*;

public class Solution {
	
	public int addDigits(int num){
		List<Integer> list = new ArrayList<Integer>();
		for(int value = num; value > 0; value /= 10){
			list.add(value%10);
		}
		int sum = 0;
		for(int i : list){
			sum += i;	
			if(sum >= 10) sum = sum/10 + sum%10;
		}
		return sum;	
	}

	public static void main(String[] argc){
		Solution s = new Solution();
		if(s.addDigits(123456789)!= 9)
			throw new IllegalStateException();			
		if(s.addDigits(56893948)!= 7)
			throw new IllegalStateException();			
	}
}
```

#### Python

```python
class Solution(object):
	def addDigits(self, num):
		l = list(str(num))	
		sum = 0
		for i in l:
			sum += int(i)
			if sum >= 10: sum = sum//10 + sum%10 #// operator is for explicit floor
		return sum

if __name__ == "__main__":
	s = Solution()
	if s.addDigits(123456789) != 9:
		raise NameError("")
	if s.addDigits(56893948) != 7:
		raise NameError("")
```
