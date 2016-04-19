---
layout: post
title: leetcode-Invert Binary Tree
description: leetcode
modified: 2016-04-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode -Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)

### Problem

One of binary tree manupulation problems

### Solution

#### Approach

Swap left and right child on every node recursively.

#### Cpp

```cpp
#include <iostream>
#include <cassert>
using namespace std;

struct TreeNode {
	int val;
	TreeNode *left;
	TreeNode *right;
	TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class BinaryTree {
public:
	BinaryTree(){
		this->root = NULL;
	}
	/* Left child tree is growing */
	void add(int val){ 
		TreeNode *tn = new TreeNode(val);
		if(this->root == NULL) { 
			this->root = tn;
			return;
		}
		TreeNode *temp = this->root;
		while(temp->left && temp->right){
			temp = temp->left;
		}
		if(temp->left == NULL) temp->left = tn;
		else if(temp->right == NULL) temp->right = tn;
	}
    int maxDepth(){
    	return maxDepth(this->root);
    }
    void preorder(){
    	preorder(this->root);
    }
    void invertTree(){
    	this->root = invertTree(this->root);
    }
private:
    int maxDepth(TreeNode* node) {
		if(node == NULL) return 0;
		int left_max = maxDepth(node->left);
		int right_max = maxDepth(node->right);
		return left_max > right_max ? left_max + 1 : right_max + 1;
    }
    void preorder(TreeNode* node) {
    	if(node == NULL) return;
    	cout << node->val;
		preorder(node->left);
		preorder(node->right);
    }
    TreeNode* invertTree(TreeNode* node) {
    	if(node == NULL) return NULL;
    	TreeNode *temp = invertTree(node->left);
    	node->left = invertTree(node->right);
    	node->right = temp;
    	return node;
    }
	TreeNode *root;
};
int main(){
	BinaryTree t; 
	for(int i = 1; i<9; i++){
		t.add(i);
	}
	t.preorder(); //must be 12468753
	t.invertTree();
	cout << endl;
	t.preorder(); //must be 13254768
	return 0;
}	
```

#### Java

```java
public class Solution {
	public Solution(){}
	public void add(int val){
		TreeNode tn = new TreeNode(val);
		if(root == null){
			root = tn;
			return;
		}
		TreeNode temp = root;
		while(temp.left != null && temp.right != null){
			temp = temp.left;
		}
		if(temp.left == null) temp.left = tn;
		else if(temp.right == null) temp.right = tn;
	}
    public int maxDepth(){
    	return maxDepth(root);
    }
    public void preorder(){
    	preorder(root);
    }	
    public void invertTree(){
    	invertTree(root);
    }
	private TreeNode root;
	private class TreeNode {
	    int val;
	    TreeNode left;
	    TreeNode right;
	    TreeNode(int x) { val = x; } //default value of member variables is null
	}
    private int maxDepth(TreeNode node) {
		if(node == null) return 0;
		int left_max = maxDepth(node.left);
		int right_max = maxDepth(node.right);
		return left_max > right_max ? left_max + 1 : right_max + 1;
    }
    private void preorder(TreeNode node){
    	if(node == null) return;
    	System.out.print(node.val);
    	preorder(node.left);
    	preorder(node.right);
    }
    private TreeNode invertTree(TreeNode node){
    	if(node == null) return null;
    	TreeNode temp = invertTree(node.left);
    	node.left = invertTree(node.right);
    	node.right = temp;
    	return node;
    }
    
    public static void main(String[] argc){
    	Solution t = new Solution();
    	for(int i = 1; i<9; i++){
    		t.add(i);
    	}
		t.preorder(); //must be 12468753
		t.invertTree();
		System.out.println();
		t.preorder(); //must be 13254768
    }
}
```

#### Python

```python
class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class BinaryTree(object):
	def __init__(self):
		self.root = None
	def add(self, val):
		tn = TreeNode(val)	
		if self.root is None:
			self.root = tn
			return
		temp = self.root
		while temp.left is not None and temp.right is not None:
			temp = temp.left
		if temp.left is None:
			temp.left = tn
		elif temp.right is None:
			temp.right = tn
	def maxDepth(self):
		return self._maxDepth(self.root)
	def preorder(self):
		self._preorder(self.root)	
	def invertTree(self):
		self._invertTree(self.root)	
	def _maxDepth(self, node):
		if node is None:
			return 0
		left_max = self._maxDepth(node.left)
		right_max = self._maxDepth(node.right)
		if left_max > right_max:
			return left_max + 1
		else: 
			return right_max + 1
	def _preorder(self, node):
		if node is None:return
		print(node.val,end='') 
		self._preorder(node.left)
		self._preorder(node.right)
	def _invertTree(self, node):
		if node is None: return None
		temp = self._invertTree(node.left)
		node.left = self._invertTree(node.right)
		node.right = temp
		return node

if __name__ == "__main__":
	bt = BinaryTree()
	for i in range(1, 9):
		bt.add(i)
	bt.preorder(); #must be 12468753
	print()
	bt.invertTree();
	bt.preorder(); #must be 13254768
```
