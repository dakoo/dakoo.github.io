---
layout: post
title: leetcode-Swap Nodes in Pairs
description: leetcode
modified: 2016-05-09
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)

### Problem

Handling linked lists

### Solution 

#### Approach

Linked lists problems tend to use recursion. To design a recursive function for this problem, take the following into consideration.

- What item must be returned (= What the caller function expects): the address of the head of each step must be returned.
- Start/Stop condition: Minimum requirements to run the function. In this case, at least two items mustn't be null. 
- Operation: swap two items and the tail item must point to the following items.

#### Complexity

O(n): traversal of a list

#### Cpp

- Solution.cpp

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};


class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(head == nullptr)
            return nullptr;      
        if(head->next == nullptr)
            return head;
        ListNode* newhead = head->next; 
        head->next = swapPairs(head->next->next);
        newhead->next = head;
        return newhead;
    }
};
```

#### Java

- ListNode.java

```java
package com.swappairs;

public class ListNode {
	int val;
	ListNode next;
	ListNode(int x) {val = x;}
}
```

- Solution.java

```java
package com.swappairs;

public class Solution {
	public ListNode swapPairs(ListNode head) {
		if(head == null || head.next == null) {
			return head;
		}
		ListNode newhead = head.next;
		head.next = swapPairs(head.next.next);
		newhead.next = head;
		return newhead;
	}
}
```

#### Python

- Solution.py

```python
class ListNode(object):

    def __init__(self, x):
        self.val = x
        self.next = None


class Solution(object):

    def swap_pairs(self, head):
        if head is None:
            return None
        if head.next is None:
            return head
        newhead = head.next
        head.next = self.swap_pairs(head.next.next)
        newhead.next = head
        return newhead

```
