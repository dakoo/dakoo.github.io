---
layout: post
title: leetcode-Reverse-Linked-List
description: leetcode
modified: 2016-04-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - reverse linked list](https://leetcode.com/problems/reverse-linked-list/)

### Problem

Very frequently asked question about the linked list. You need to have deep understanding of iterative and recursive concepts.

### Solution 

#### Approach

Either of iterative and recursive

#### Iterative


Revert the nodes from the first node of a linked list in a iterative manner. newhead, a head head of reverted list is initialized with NULL.

1 Repeat 2-5 to iterate the items of a list until the end of the list (Item is null).
2. Store the next item temporarily: temp = current->next
3. Make current's next point to newhead: current->next = newhead
4. Make the current the newhead: newhead = current
5. Move to the next: current = temp
6. Finally, return the newhead. 

```
[Original]
head -> 1 -> 2 -> 3  -> 4 -> 5 ->NULL

[Snapshot during iterative processing]
(Step 1)
NULL <- 1 <- 2    3  ->  4 -> 5-> NULL
             ^    ^      ^ 
	      newhead current  temp

(Step 2)
NULL <- 1 <- 2<-  3      4 -> 5-> NULL
             ^    ^      ^ 
	     newhead current  temp

(Step 3)
NULL <- 1 <- 2<-  3      4 -> 5 -> NULL
                  ^      ^ 
	   newhead/current  temp

(Step 4)
NULL <- 1 <- 2<-  3      4 -> 5 -> NULL
                  ^      ^ 
	         newhead    current/temp
```

#### Recursive

Revert the nodes from the last node of a linked list in a recursive manner. Each level of the recursion is sure that the next item of the node is reverted. Then it makes the next of the next node point to the node and the next of the node must be NULL.

1. Invoke a recursive call with the pointer to next node until the node and the node's next are NOT NULL. The function returns the head of the reverted list :  newhead = call(current->next);
2. Make the next of the current's next node point to the current node: current->next->next = current
3. Make the next of the current node point to the NULL because it must be the tail of the reverted list : current -> next = NULL  
4. Finally, return the newhead

```
[Original]
head -> 1 -> 2 -> 3  -> 4 -> 5 -> NULL

[Snapshot during recursive processing]
(Before Step 1: call with current->next)
head -> 1 -> 2 -> 3 -> 4 -> 5 -> NULL
                  ^    ^     
	          current    

(After Step 2)
head -> 1 -> 2 -> 3   NULL<- 4 <- 5 
                  ^               ^    
	          current               newhead

(Step 2)
head -> 1 -> 2 -> 3 <- 4 <- 5
                  ^         ^     
	        current         newhead

(Step 3)
head -> 1 -> 2   NULL <- 3 <-  4 <- 5 
                         ^          ^     
	                   current      newhead
```

#### Complexity

O(N)


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

class SinglyLinkedList {
private:
	ListNode *head;
    ListNode* reverseList_iterative(ListNode* head) {
    	ListNode* newhead = NULL;	
    	ListNode* current = head;
    	if(current == NULL) return newhead;
    	while(current){
	    	ListNode* temp = current->next;
	    	current->next = newhead;
	    	newhead = current;
	    	current = temp;
    	}
    	return newhead;
    }
    ListNode* reverseList_recursive(ListNode* node) {
		if(node == NULL) return node; //empty list
		ListNode *current = node;
		if(current->next == NULL) return current; //the end of the list
    	ListNode* newhead = reverseList_recursive(current->next);	
    	current->next->next = current;
    	current->next = NULL;
    	return newhead;
    }
    void clear(){
		ListNode *temp = head;
		while(temp){
			ListNode *prev = temp;
			temp = temp->next;
			delete(prev);
		}
		head = NULL;
    }
public:
	SinglyLinkedList(){ head = NULL;}
	~SinglyLinkedList(){ clear();}
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
	void reverseList_recursive(){
		head = reverseList_recursive(head);	
	}
	void reverseList_iterative(){
		head = reverseList_iterative(head);	
	}
};
int main(){
	vector<vector<int> >testcases = {
		{},
		{1},
		{1,2},
		{1,2,3,4,5,6},
		{-3,-2,-1,0,1,2,3},};
	for (auto t : testcases){
		SinglyLinkedList sll;
		for(auto i : t){
			sll.add(i);	
		}
		cout << "Original:\t\t\t\t\t";
		sll.display();
		sll.reverseList_recursive();
		cout << "After revert recursively:\t";
		sll.display();
		sll.reverseList_iterative();
		cout << "After revert iteratively:\t";
		sll.display();
		cout << "------------------"<<endl;
	}
	return 0;
}
```

#### Java

```java
import java.util.*;
public class SinglyLinkedList {
	public class ListNode {
		int val;
		ListNode next;
		ListNode(int x) { val = x; }
	}
	ListNode head;
    private ListNode reverseList_iterative(ListNode head) {
    	if(head == null) return null;
    	ListNode newhead = null;
    	ListNode current = head;
    	while(current != null){
    		ListNode temp = current.next;
    		current.next = newhead;
    		newhead = current;
    		current = temp;
    	}
    	return newhead;
    }
    private ListNode reverseList_recursive(ListNode node) {
		if(node == null) return null; //empty        
		ListNode current = node;
		if(current.next == null) return current; //end of the list
		ListNode newhead = reverseList_recursive(current.next);
		current.next.next = current;
		current.next = null;
		return newhead;
    }
	SinglyLinkedList(){
		head = null;
	}
    public void reverseList_recursive(){
    	head = reverseList_recursive(head);
    }
    public void reverseList_iterative(){
    	head = reverseList_iterative(head);
    }
    public void add(int val){
    	ListNode node = new ListNode(val);
    	if(head == null){
    		head = node;
    		return;
    	}
    	node.next = head;
    	head = node;
    }
    public void display(){
    	ListNode temp = head;
    	while(temp != null){
    		System.out.print(temp.val);
    		temp = temp.next;
    	}	
    	System.out.println("");
    }
    public void clear(){
    	head = null;
    }
    public static void main(String[] argc){
    	int[][] testcases = {
			{},
			{1},
			{1,2},
			{1,2,3,4,5,6},
			{-3,-2,-1,0,1,2,3},};
		SinglyLinkedList sll = new SinglyLinkedList();	
		for( int[] t: testcases){
			for(int i : t){
				sll.add(i);
			}
			System.out.print("Original:\t\t\t");
			sll.display();
			sll.reverseList_recursive();
			System.out.print("After revert recursively:\t");
			sll.display();
			sll.reverseList_iterative();
			System.out.print("After revert iteratively:\t");
			sll.display();
			System.out.println("------------------");
			sll.clear();
		}
    }
}
```

#### Python

```python
class ListNode(object):
	def __init__(self, x):
		self.val = x
		self.next = None

class SinglyLinkedList(object):
	def __init__(self):
		self.head = None
	def _reverseList_iterative(self, node):
		current = node 
		newhead = None
		while current != None :
			temp = current.next
			current.next = newhead
			newhead = current 
			current = temp
		return newhead       
	def reverseList_iterative(self):
		self.head = self._reverseList_iterative(self.head)
	def _reverseList_recursive(self,node):
		if node == None: return None
		current = node
		if current.next == None: 
			return current 
		newhead = self._reverseList_recursive(current.next)
		current.next.next = current 
		current.next = None
		return newhead
	def reverseList_recursive(self):
		self.head = self._reverseList_recursive(self.head)
	def add(self, val):
		node = ListNode(val)
		if self.head == None :
			self.head = node
		else:   
			node.next = self.head
			self.head = node
	def display(self):
		temp = self.head
		while temp != None:
			print(temp.val, end="")
			temp = temp.next
		print("")
	def clear(self):
		self.head = None   

if __name__ == "__main__":
	testcases =  [[],
				[1],
				[1,2],
				[1,2,3,4,5,6],
				[-3,-2,-1,0,1,2,3],];
	sll = SinglyLinkedList();
	for t in testcases:
		for i in t:
			sll.add(i)	
		print("Original:\t\t\t\t\t", end="")
		sll.display()
		sll.reverseList_recursive()
		print("After revert recursively:\t", end="")
		sll.display()
		sll.reverseList_iterative()
		print("After revert iteratively:\t", end="")
		sll.display()
		sll.clear()
		print("------------------")
```
