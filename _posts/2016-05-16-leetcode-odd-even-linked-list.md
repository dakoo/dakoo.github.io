---
layout: post
title: leetcode-odd even linked list
description: leetcode
modified: 2016-05-16
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - odd even linked list](https://leetcode.com/problems/odd-even-linked-list/)

### Problem

Manipulating linked lists

### Solution 

#### Approach

Make 2 linked lists to store odd and even nodes with four temporary pointers as below:

- head of odd linked list
- tail of odd linked list
- head of even linked list
- tail of even linked list

Then, connect two lists. 

#### Complexity

Time complexity: O(N) 
Space complexity: O(1)

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
    ListNode* oddEvenList(ListNode* head) {
        if(head == nullptr || head->next == nullptr)
            return head;
        ListNode* odd = head;
        ListNode* odd_tail = head;
        head = head->next;
        ListNode* even = head;
        ListNode* even_tail = head;
        head = head->next;
        auto index = 1;
        while(head){
            if (index % 2) {
                odd_tail->next = head;
                odd_tail = odd_tail->next;
            } else {
                even_tail->next = head;
                even_tail = even_tail->next;
            } 
            index++;
            head = head->next;
        }
        odd_tail->next = even;
        even_tail->next = nullptr;
        return odd;
    }
};
```
