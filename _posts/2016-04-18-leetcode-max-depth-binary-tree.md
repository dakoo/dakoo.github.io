---
layout: post
title: leetcode-maximum depth of binary tree
description: leetcode
modified: 2016-04-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - maximum depth of binary tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

### Problem

One of binary tree handling problems.  

### Solution

#### Approach

Recusion is the easiest to figure out the maximum depth of a binary tree. The return value of a node is the max depth of the node. That is, we need to take bigger one from max depth of left and right child.

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
private:
    int maxDepth(TreeNode* node) {
		if(node == NULL) return 0;
		int left_max = maxDepth(node->left);
		int right_max = maxDepth(node->right);
		return left_max > right_max ? left_max + 1 : right_max + 1;
    }
	TreeNode *root;
};
int main(){
	BinaryTree t; 
	t.add(1);
	t.add(2);
	t.add(3);
	t.add(4);
	t.add(5);
	t.add(6);
	t.add(7);
	t.add(8);
	assert(t.maxDepth() == 5);
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

    public static void main(String[] argc){
    	Solution t = new Solution();
		t.add(1);
		t.add(2);
		t.add(3);
		t.add(4);
		t.add(5);
		t.add(6);
		t.add(7);
		t.add(8);
		if(t.maxDepth() !=5)
			throw new IllegalStateException();
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
	def _maxDepth(self, node):
		if node is None:
			return 0
		left_max = self._maxDepth(node.left)
		right_max = self._maxDepth(node.right)
		if left_max > right_max:
			return left_max + 1
		else: 
			return right_max + 1

if __name__ == "__main__":
	bt = BinaryTree()
	bt.add(1)
	bt.add(2)
	bt.add(3)
	bt.add(4)
	bt.add(5)
	bt.add(6)
	bt.add(7)
	bt.add(8)
	if bt.maxDepth() != 5: raise NameError("")
```
