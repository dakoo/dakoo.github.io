---
layout: post
title: leetcode-add two numbers
description: leetcode
modified: 2016-04-30
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - add two numbers](https://leetcode.com/problems/add-two-numbers/)

### Problem

Handling linked lists. An example of a linked list which is the digits are stored in reverse order: 

> 342: 2->4->3 

### Solution 

#### Approach

While traversal fo a linked list from the head, sum two numbers. If the sum is larger than 10, move it the next step. To avoid memory leak, use fake head. 

#### Complexity

O(n)

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
struct LinkedList {
	ListNode *head;
	void insert(int v){
		ListNode* node = new ListNode(v);
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
	LinkedList(){
		head = NULL;
	}
};
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    	ListNode *fakehead = new ListNode(0); 
    	ListNode *temp = fakehead;
    	int sum = 0;
    	while(l1 || l2 || sum){
    		if(l1){
	    		sum += l1->val; 
	    		l1 = l1->next;
	    	}
	    	if(l2){
	    		sum += l2->val;
	    		l2 = l2->next;
	    	}
	    	ListNode* node = new ListNode(sum%10);
	    	temp->next = node;
	    	temp = node;
    		sum = sum/10; 
    	}
	    temp = fakehead->next;
	    delete fakehead;
	    return temp; 
    }
};

int main(){
	vector<int> test1 = {3, 4, 2};
	vector<int> test2 = {4, 6, 5};
	LinkedList l1;
	LinkedList l2;
	for(auto i: test1){
		l1.insert(i);
	}
	for(auto i: test2){
		l2.insert(i);
	}
	Solution so;
	l1.display();
	l2.display();
	LinkedList rl;	
	rl.head = so.addTwoNumbers(l1.head, l2.head);
	rl.display();
	return 0;
}
```

#### Java

```java
public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
		ListNode fakehead = new ListNode(0);        
		ListNode temp = fakehead;
		int sum = 0;
		while(l1 != null || l2 != null || sum != 0){
			if(l1 != null){
				sum += l1.val;
				l1 = l1.next;
			}		
			if(l2 != null){
				sum += l2.val;
				l2 = l2.next;
			}		
			ListNode node = new ListNode(sum % 10);
			temp.next = node;
			temp = node;
			sum = sum / 10;
		}
		return fakehead.next;
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
    def addTwoNumbers(self, l1, l2):
    	fakehead = ListNode(0)	
    	temp = fakehead
    	sum = 0
    	while l1 != None or l2 != None or sum != 0:
    		if l1 != None: 
    			sum += l1.val
    			l1 = l1.next
    		if l2 != None: 
    			sum += l2.val
    			l2 = l2.next
    		node = ListNode(sum % 10)
    		temp.next = node
    		temp = node
    		sum = sum / 10
    	return fakehead.next
```
