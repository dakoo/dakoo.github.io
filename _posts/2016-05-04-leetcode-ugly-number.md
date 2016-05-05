---
layout: post
title: leetcode-ugly number
description: leetcode
modified: 2016-05-04
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - ugly number](https://leetcode.com/problems/ugly-number/)

### Problem

A puzzle handling numbers.

### Solution 


Don't be captivated by the word 'prime number'. It's not a problem to determine if the numbers are prime numbers. 
Instead, it's a problem to determine if a given number is divided by 2, 3 and 5. 

#### Approach

Let's take steps below. Note that 2/2 = 1, 3/3 = 1, and 5/5 = 1.

1. Divide a number by 2 until the remainder is not divided by 2.
2. Divide a number by 2 until the remainder is not divided by 3.
3. Divide a number by 2 until the remainder is not divided by 5.
4. The remainder is 0 or 1, the given number is an ugly number.

#### Complexity

O(1)

#### Cpp

[CATCH](https://github.com/philsquared/Catch/blob/master/docs/tutorial.md) used as a cpp unit-test framework.

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
#include <vector>

using namespace std;

class Solution {
public:
    bool isUgly(int num) {
    	if(num <= 0) return false;
    	vector<int> T = {2, 3, 5};
    	for(auto t : T){
    		while(num % t == 0){
    			num = num / t;	
    		}
    	}
    	return num == 1;
    }
};

TEST_CASE( "Ugly values", "[Solution]" ){
	Solution so;
	SECTION("6 should be ugly"){
		REQUIRE(so.isUgly(6));
	}
	SECTION("8 should be ugly"){
		REQUIRE(so.isUgly(8));
	}
}

TEST_CASE( "None-ugly values", "[Solution]"  ){
	Solution so;
	SECTION("14 should be not ugly"){
		REQUIRE_FALSE(so.isUgly(14));
	}
}

TEST_CASE( "Boundary values: [0, negative numbers, 1]", "[Solution]" ){
	Solution so;
	SECTION("0 should be not ugly"){
		REQUIRE_FALSE(so.isUgly(0));
	}
	SECTION("-1 should be not ugly"){
		REQUIRE_FALSE(so.isUgly(-1));
	}
	SECTION("1 should be ugly"){
		REQUIRE(so.isUgly(1));
	}
}
```

#### Java

```java
public class LinkedList {
    public class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
            next = null;
        }
    }
	private ListNode head;
	public LinkedList(){
		head = null;
	}
	public void add(int v){
        ListNode node = new ListNode(v);
        node.next = head;
        head = node;
	}
    private boolean hasCycle(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while(slow != null && fast != null){ 
            fast = fast.next;
            if(fast == null) return false;
            if(fast == slow) return true;
            fast = fast.next;
            if(fast == null) return false;
            if(fast == slow) return true;
            slow = slow.next;
        }
        return false;
    }
    public boolean hasCycle() {
        return hasCycle(head);  
    }
    public void makeCycle(){
        ListNode temp = head;
        ListNode prev = head;
        while(temp != null){ 
            prev = temp;
            temp = temp.next;
        }
        if(prev != null)
            prev.next = head;
    }
    public static void main(String[] argc){
        int[] nums = {1, 2, 3, 4};
        LinkedList ll = new LinkedList();
        for(int i : nums){
            ll.add(i);
        }
        if(ll.hasCycle())
            System.out.println("Cycle");
        else 
            System.out.println("No cycle");
        ll.makeCycle();
        if(ll.hasCycle())
            System.out.println("Cycle");
        else 
            System.out.println("No cycle");
    }
}
```

#### Python

unittest used as a unittest framework.

```python
class Solution(object):
    def isUgly(self, num):
        if num <= 0: return False
        for t in [2, 3, 5]:
            while num % t == 0:
                num = num / t
        return num == 1

import unittest

class TestUM(unittest.TestCase): 
    def setUp(self):
        self.so = Solution()

    def test_ugly_values(self):
        for t in [6, 8]:
            self.assertEqual( self.so.isUgly(t), True)

    def test_none_ugly_values(self):
        for t in [14]:
            self.assertEqual( self.so.isUgly(t), False)

    def test_boundary_ugly_values(self):
        for t in [1]:
            self.assertEqual( self.so.isUgly(t), True)

    def test_boundary_none_ugly_values(self):
        for t in [-1, 0]:
            self.assertEqual( self.so.isUgly(t), False)

if __name__ == "__main__":
    unittest.main()
```
