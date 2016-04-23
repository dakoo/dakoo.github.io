---
layout: post
title: leetcode-same tree
description: leetcode
modified: 2016-04-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - same tree](https://leetcode.com/problems/same-tree/)

### Problem

One of binary tree problems

### Solution

#### Approach

Recursively visit all nodes and compare the value. Check the two nodes themself. Then, if the left and right child return true, they are same.

#### Cpp

```cpp
#include <iostream>

using namespace std;

struct TreeNode {
	int val;
	TreeNode *left;
	TreeNode *right;
	TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
class Solution {
public:
...
    bool isSameTree(TreeNode* p, TreeNode* q) {
    	if(p == NULL && q == NULL) return true;
    	else if(p == NULL || q == NULL) return false;
    	if(p->val != q->val) return false;
    	return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

#### Java

```java
public class TreeNode {
	int val;
	TreeNode left;
	TreeNode right;
	TreeNode(int x) { val = x; }
}
public class Solution {
...
    public boolean isSameTree(TreeNode p, TreeNode q) {
		if(p == null && q == null) return true;        
		if(p == null || q == null) return false;
		if(p.val != q.val) return false;
		return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
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

class Solution(object):
...
    def isSameTree(self, p, q):
    	if p == None and q == None : return True
    	if p == None or q == None : return False
    	if p.val != q.val : return False
    	return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)

```
