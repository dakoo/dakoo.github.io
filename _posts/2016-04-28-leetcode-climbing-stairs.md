---
layout: post
title: leetcode-climbing stairs
description: leetcode
modified: 2016-04-26
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - climbing stairs](https://leetcode.com/problems/climbing-stairs/)

### Problem

Counting ways to reach to some place.

### Solution 

#### Approach

Dynamic Programming: the ways to reach nth stair is the sum of the ways to reach (n-1)th stair and (n-2)th stair.

> S[n] = S[n - 1] + S[n - 2]

Initial Values of S[0] and S[1] are 0.

#### Complexity

- Time complexity: O(N), To traverse the stairs.  
- Space complexity: O(1), using 3 variables instead of lists or arrays.

#### Cpp

```cpp
#include <iostream>

using namespace std;

class Solution {
public:
    int climbStairs(int n) {
        if(n == 0 || n == 1) return 1;
        int S0 = 1; 
        int S1 = 1;
        int S2 = 0;
        for(int i = 2; i<= n; i++){
            S2 = S1 + S0;
            S0 = S1;
            S1 = S2;
        }
        return S2;
    }
};
int main(){
    Solution so;
    int testcase = 10;
    cout << so.climbStairs(testcase); //must be 89
    return 0;
}
```

#### Java

```java
public class Solution {
    public int climbStairs(int n) {
 		if(n == 0 || n == 1) return 1; 
 		int S0 = 1;
 		int S1 = 1;
 		int S2 = 0;
 		for(int i = 2; i<= n; i++){
 			S2 = S1 + S0;
 			S0 = S1;
 			S1 = S2;
 		}	
 		return S2;
    }
    public static void main(String[] argc){
    	int testcase = 10;	
    	Solution so = new Solution();
    	System.out.println(so.climbStairs(testcase)); //must be 89
    }
}
```

#### Python

```python
class Solution(object):
    def climbStairs(self, n):
        if n == 0 or n == 1:
            return 1
        S0, S1, S2 = 1, 1, 0
        for i in range(2, n+1):
            S2 = S1 + S0
            S1, S0 = S2, S1
        return S2

if __name__ == "__main__":
    testcase = 10
    so = Solution()
    print(so.climbStairs(10))   #must be 89
```
