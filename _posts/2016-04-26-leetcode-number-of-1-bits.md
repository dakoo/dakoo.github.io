---
layout: post
title: leetcode-Number of 1 Bits
description: leetcode
modified: 2016-04-26
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)

### Problem

Convert a decimal number into a binary number. Then count '1's.

### Solution 

#### Approach

Use bit operations to implement a function to convert decimal to binary as below.

1. Repeat 2 to 4 until the decimal number, N is not 0
2. Divide the N by 2. 
3. Then, the remainder is 1, increase the counter.
4. N = the quotient

##### No bitwise operations

```cpp
    int hammingWeight(uint32_t n) {
        int counter = 0;
        while(n != 0){
            if(n%2) counter++;
            n = n/2;
        } 
        return counter;
    }
```

##### Bitwise operations

```cpp
    int hammingWeight(uint32_t n) {
        int counter = 0;
        while(n != 0){
            if(n & 0x1) counter++;
            n = n >> 0x1;
        } 
        return counter;
    }
```

#### Complexity

O(1) : Because N is 32 bits. 

#### Cpp

```cpp
#include <iostream>

using namespace std;

class Solution {
public:
    int hammingWeight(uint32_t n) {
        int counter = 0;
        while(n != 0){
            if(n & 0x1) counter++;
            n = n >> 0x1;
        } 
        return counter;
    }
};

int main(){
    uint32_t testcase = 11;
    Solution so;
    cout << so.hammingWeight(testcase) << endl;
    return 0;
}
```

#### Java

Bitwise operators in Java are little different from C++. Use >>> to fill the MSB with '0' instead of >>. 
'&' must not be used in 'if' statement.

```java
public class Solution {
    public int hammingWeight(int n) {
      int counter = 0;
      while(n != 0){
        int temp = n & 0x1;
        if(temp == 0x1) counter++;
        n = n >>> 0x1;
      }
      return counter; 
    }
    public static void main(String[] argc){
      int testcase = 11;
      Solution so = new Solution();
      System.out.println(so.hammingWeight(testcase));
    }
} 
```

#### Python

```python
class Solution(object):
    def hammingWeight(self, n):
        counter = 0;
        while n != 0:
            if n & 0x1: counter += 1
            n = n >> 0x1
        return counter

if __name__ == "__main__":
    testcase = 11 
    so = Solution()
    print(so.hammingWeight(testcase))
```
