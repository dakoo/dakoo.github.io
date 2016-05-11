---
layout: post
title: leetcode-Merge Two Sorted Lists
description: leetcode
modified: 2016-05-08
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

### Problem

Handling linked lists

### Solution 

#### Approach

Linked lists problems tend to use recursion. To design a recursive function for this problem, take the following into consideration.

- What item must be returned (= What the caller function expects): the address of the head of each step must be returned.
- Start/Stop condition: Minimum requirements to run the function. In this case, at least two lists mustn't null. If both of them is null, return null. If either of them is null, return none-null list.

#### Complexity

O(n): traversal of two lists

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
    ListNode* mergeTwoLists(ListNode* first_list, ListNode* second_list) {
        if(first_list == nullptr && second_list == nullptr){
            return nullptr;
        } else if(first_list == nullptr){
            return second_list;
        } else if(second_list == nullptr){
            return first_list;
        }
        if(first_list->val < second_list->val){
            first_list->next = mergeTwoLists(first_list->next, second_list);
            return first_list;
        }
        second_list->next = mergeTwoLists(first_list, second_list->next);
        return second_list;
    }
};
```

#### Java

- ListNode.java

```java
package com.mergetwolists;

public class ListNode {
	int val;
	ListNode next;
	ListNode(int x){val = x;}
}
```

- Solution.java

```java
package com.mergetwolists;

public class Solution {
    public ListNode mergeTwoLists(ListNode firstList, ListNode secondList) {
    	if(firstList == null && secondList == null) {
    		return null;
    	}
    	if(firstList == null) {
    		return secondList;
    	}
    	if(secondList == null) {
    		return firstList;
    	}
    	if(firstList.val < secondList.val) {
    		firstList.next = mergeTwoLists(firstList.next, secondList);
    		return firstList;
    	}
    	secondList.next = mergeTwoLists(firstList, secondList.next);
		return secondList;
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

    def mergetwolists(self, first_sorted_list, second_sorted_list):
        if first_sorted_list is None and second_sorted_list is None:
            return None
        if first_sorted_list is None:
            return second_sorted_list
        elif second_sorted_list is None:
            return first_sorted_list
        if first_sorted_list.val < second_sorted_list.val:
            first_sorted_list.next = self.mergetwolists(
                first_sorted_list.next, second_sorted_list)
            return first_sorted_list
        else:
            second_sorted_list.next = self.mergetwolists(
                first_sorted_list, second_sorted_list.next)
            return second_sorted_list

```
