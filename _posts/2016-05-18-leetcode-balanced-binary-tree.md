---
layout: post
title: leetcode-Balanced Binary Tree
description: leetcode
modified: 2016-05-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)

### Problem

Manipulating tree recursively. 
Difficult point of this problem is that the required prototype is not enough for solutions.

### Solution 

#### Approach

Let's design a solution without the consideration the prototype defined in this problem as follows:

- The following function returns the height of the node if the node is balanced. If the node is not balanced, return -1

```python
def getHeight(node): 
	if node is None:
		return 0
	left_height = getHeight(node.left)
	if left_height == -1:
		return left_height
	right_height = getHeight(node.right)
	if right_height == -1:
		return right_height
	if abs(left_height - right_height) > 2 : 
		return -1
	return max(left_height, right_height) + 1	

```

Then, add the above function in the original prototype as below.

```python
def isBalanced(root):
	if getHeight(root) == -1 : 
		return False
	return True

```

#### Complexity

Time complexity: O(N)  

#### Cpp

- abs(): include cstdlib 
- max(): include algorithm 

- Solution.hpp

```cpp
struct TreeNode {
	int val;
	TreeNode *left;
	TreeNode *right;
	TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
class Solution {
private:
	int getHeight(TreeNode* node);
public:
	bool isBalanced(TreeNode* root);
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <algorithm>
#include <cstdlib>

using namespace std;

int Solution::getHeight(TreeNode* node) {
    if (node == nullptr)
        return 0;
    auto left_height = getHeight(node->left);
    if (left_height == -1)
        return -1;
    auto right_height = getHeight(node->right);
    if (right_height == -1)
        return -1;
    if (abs(left_height - right_height) > 1)
        return -1;
    return max(left_height, right_height) + 1;
}
bool Solution::isBalanced(TreeNode* root) {
    if (getHeight(root) == -1)
        return false;
    return true;
}

```
