---
layout: post
title: leetcode-Symmetric Tree
description: leetcode
modified: 2016-05-16
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)

### Problem

Manipulating a binary tree. Note that the symmetric tree doesn't mean the left and right child have a same value. 

For example, the following tree is symmetric:

```
    1
   / \
  2   2
 /     \
3       3
```

However, this tree is not symmetric:

```
    1
   / \
  2   2
   \   \
   3    3
```

Both of recursive and iterative solutions are necessary. 

### Solution 

#### Approach

We have to compare the nodes under different subtrees. 
So, we need an algorithm to carry on the following: 

- Subtree1 and subtree2 are symmetric. 
- Compare the left child of a root node in subtree1 and the right child of a root node in subtree2.  
- Compare the right child of a root node in subtree1 and the left child of a root node in subtree2. 

##### Recursive Approach

```
def isSymmetric( head_subtree1, head_subtree2 ):
	if head_subtree1 is None and head_subtree1 is None:
		return true
	if head_subtree1 is None or head_subtree1 is None:
		return false
	if head_subtree1.val != head_subtree2.val:
		return false
	return isSymmetric(head_subtree1.left, head_subree2.right) && isSymmtric(head_subtree1.right, head_subtree2.left) 
```

##### Iterative Approach

We can traverse a tree iteratively using stack. An item in the stack should contains subtree1 and subtree2.

```
def isSymmetic(root):
	if root is None:
		return True
	stack = [];
	stack.append([root.left, root.right])
	while len(stack)!= 0:
		two_nodes = stack.pop()
		if two_nodes[0] is None and two_nodes[1] is None:
			continue
		if two_nodes[0] is None or two_nodes[1] is None:
			return False
		if two_nodes[0].val != two_nodes[1].val:
			return False
		stack.append([two_nodes[0].left, two_nodes[1].right]);
		stack.append([two_nodes[0].right, two_nodes[1].left]);
	return True
```

#### Complexity

Time complexity: O(N) 

#### Cpp

* Check how to use the following vector functions and auto keyword.

- emplace_back()
- empty()
- back()
- pop_back()

##### Recursive 

- Solution.hpp

```
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
private:
	bool _isSymmetric(TreeNode *left, TreeNode *right);
public:
    bool isSymmetric(TreeNode* root);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"

bool Solution::_isSymmetric(TreeNode *first_head, TreeNode *second_head){
	if(first_head == nullptr && second_head == nullptr)
		return true;
	if(first_head == nullptr || second_head == nullptr)
		return false;
	if(first_head->val != second_head->val)
		return false;
	return _isSymmetric(first_head->left, second_head->right) && _isSymmetric(first_head->right, second_head->left);
}
bool Solution::isSymmetric(TreeNode* root){
	if(root == nullptr)
		return true;
	return _isSymmetric(root->left, root->right);
}
```

##### Iterative 

- Solution.hpp

```
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
public:
    bool isSymmetric(TreeNode* root);
};
```

- Solution.cpp

```cpp
#include <vector>
#include <algorithm>
#include "Solution.hpp"

using namespace std;

bool Solution::isSymmetric(TreeNode* root){
	if(root == nullptr)
		return true;
	vector<pair<TreeNode*, TreeNode*> > stack;
	stack.emplace_back(root->left, root->right);
	while(!stack.empty()){
		auto two_nodes = stack.back();
		stack.pop_back();
		if(two_nodes.first == nullptr && two_nodes.second == nullptr)
			continue;
		if(two_nodes.first == nullptr || two_nodes.second == nullptr)
			return false;
		if(two_nodes.first->val != two_nodes.second->val)
			return false;
		stack.emplace_back(two_nodes.first->left, two_nodes.second->right);
		stack.emplace_back(two_nodes.first->right, two_nodes.second->left);
	}
	return true;    
}
```
