---
layout: post
title: leetcode-linked list cycle
description: leetcode
modified: 2016-05-01
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - linked list cycle](https://leetcode.com/problems/linked-list-cycle/)

### Problem

Handling linked lists. A key of this problem is you should do this without any extra space.

### Solution 

#### Approach

First of all, don't think of the linked list you are handlig as infinite items. It's finite. 
Second, you can traverse the linked list in two different ways. One is slow, that is one by one. The other is fast, that is you move through several items once. If there is cycle, both of them  will meet at some node. Otherwise, either of them arrives at the end node of the linked list.

#### Complexity

O(n): traversal of linked list

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
    bool hasCycle(ListNode *head) {
    	ListNode* slow = head;
    	ListNode* fast = head;
   		while(slow && fast){
   			for(int i = 0; i<3; i++){
	   			fast = fast->next;
	   			if(fast == NULL) return false;
	   			if(slow == fast) return true;
   			}
   			slow = slow->next;
   		}
		return false;			        
    }
public:
	LinkedList(): head(NULL){}
    void add(int v){
    	ListNode *node = new ListNode(v);
    	node->next = head;
    	head = node;
    }
    void makeCycle(){
    	ListNode* temp = head;
    	ListNode* prev = temp;
    	while(temp){
    		prev = temp;
    		temp = temp->next;
    	}
    	if(prev) 
    		prev->next = head;
    }
    bool hasCycle(){
    	return hasCycle(head);
    }
};
int main(){
	vector<int> nums = {1, 2, 3, 4};
	LinkedList ll;
	for(auto i : nums){
		ll.add(i);
	}
	if(ll.hasCycle())
		cout << "Cycle" << endl;
	else 
		cout << "No cycle" << endl;
	ll.makeCycle();
	if(ll.hasCycle())
		cout << "Cycle" << endl;
	else 
		cout << "No cycle" << endl;
	return 0;
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

```python
class ListNode(object):
	def __init__(self, x):
		self.val = x
		self.next = None

class LinkedList(object):
	def __init__(self):
		self.head = None

	def add(self, v):
		node = ListNode(v)
		node.next = self.head
		self.head = node

	def makeCycle(self):
		temp = self.head
		prev = temp
		while temp != None:
			prev = temp
			temp = temp.next
		if prev != None:
			prev.next = self.head

	def _hasCycle(self, head):
		slow = head
		fast = head
		while slow != None:
			fast = fast.next
			if fast == None: return False
			if fast is slow: return True
			fast = fast.next
			if fast == None: return False
			if fast is slow: return True
			slow = slow.next
		return False

	def hasCycle(self):
		return self._hasCycle(self.head)

if __name__ == "__main__":
	ll = LinkedList()
	for i in [1, 2, 3, 4]:
		ll.add(i)	
	if ll.hasCycle():	
		print("Cycle")
	else:
		print("No cycle")
	ll.makeCycle()
	if ll.hasCycle():	
		print("Cycle")
	else:
		print("No cycle")
```
