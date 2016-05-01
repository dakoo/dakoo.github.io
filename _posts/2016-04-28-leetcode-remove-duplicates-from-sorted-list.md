---
layout: post
title: leetcode-remove duplicates from sorted list
description: leetcode
modified: 2016-04-26
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - remove duplicates from sorted list](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)

### Problem

Handling linked list problems

### Solution 

#### Approach

If a value of a node's next is the same as a value of the node, remove the node's next. Note that if the next node is deleted, don't move forward. Just stay and handle the next of the deleted one.

```bash
while(node && node->next){
    if(node->next->val == node->val) {
        ListNode* temp = node->next;
        node->next = node->next->next;
        delete temp;
    } else {
        node = node->next;
    }
}
```

#### Complexity

O(N): To traverse the linked list 

#### Cpp

```cpp
#include <iostream>
#include <vector>

using namespace std;

struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};
class LinkedList {
private:
    ListNode *head;
    void clear(){
        ListNode *temp = head;
        while(temp){
            ListNode *prev = temp;
            temp = temp->next;
            delete(prev);
        }
        head = NULL;
    }
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* node = head;
        while(node && node->next){
            if(node->next->val == node->val) {
                ListNode* temp = node->next;
                node->next = node->next->next;
                delete temp;
            } else {
                node = node->next;
            }
        }
        return head;
    }
public:
    LinkedList(){ head = NULL;}
    ~LinkedList(){ clear();}
    void deleteDuplicates(){
        head = deleteDuplicates(head);
    }
    void add(int val){
        ListNode *node = new ListNode(val);
        if(head == NULL){
            head = node;
            return;
        }   
        node->next = head;
        head = node;
    }
    void display(){
        ListNode *temp = head;
        while(temp){
            cout << temp->val;
            temp = temp->next;
        }
        cout << endl;
    }
};
int main(){
    vector<vector<int> >testcases = {
        {},
        {1},
        {2, 1},
        {2, 2, 1},
        {3,3,3,3,2,2,1,1},};
    for (auto t : testcases){
        LinkedList ll;
        for(auto i : t){
            ll.add(i); 
        }        
        cout << "Original:\t\t\t\t\t\t";
        ll.display();
        ll.deleteDuplicates();
        cout << "After deletion of duplicates:\t";
        ll.display();    
        cout << "----------------" << endl;
    }
    return 0;
}
```

#### Java

```java
public class LinkedList {
	public class ListNode{
		int val;
		ListNode next;
		ListNode(int x){val = x;}
 	} 
	pubic ListNode deleteDuplicates(ListNode head){
		ListNode node = head;
		while(node != null && node.next != null){
			if(node.next.val == node.val){
				node.next = node.next.next;				
			} else {
				node = node.next;
			}
		}
		return head;
	}
}
```

#### Python

```python
class ListNode(object):
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution(object):
    def deleteDuplicates(self, head):
        node = head
        while node != None and node.next != None :
            if node.next.val == node.val:
                node.next = node.next.next
            else:
                node = node.next
        return head
```
