---
layout: post
title: leetcode-Binary Tree Inorder Traversal
description: leetcode
modified: 2016-05-24
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

### Problem

Inorder Binary tree traversal.

```
   1
  /  \
 2    3
     /
    4
```

Inorder: 2 - 1- 4- 3

### Solution 

#### Approach

Recursion

#### Complexity

Time complexity: O(N)

#### Cpp

- Solution.hpp

```cpp
#include <vector>

struct TreeNode {
	int val;
	TreeNode *left;
	TreeNode *right;
	TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
private:
	void _inorderTraversal(std::vector<int> &v, TreeNode* node);
public:
	std::vector<int> inorderTraversal(TreeNode* root);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

void Solution::_inorderTraversal(std::vector<int> &v, TreeNode* node){
	if(node == nullptr)
		return;
	_inorderTraversal(v, node->left);
	v.emplace_back(node->val);	
	_inorderTraversal(v, node->right);
}

std::vector<int> Solution::inorderTraversal(TreeNode* root){
	std::vector<int> ret;
	_inorderTraversal(ret, root);_
	return ret;
}

```
