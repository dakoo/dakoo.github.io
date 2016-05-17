---
layout: post
title: leetcode-path sum
description: leetcode
modified: 2016-05-16
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - path sum](https://leetcode.com/problems/path-sum/)

### Problem

Binary tree problem

### Solution 

#### Approach

Top-down approach. Subtract the value of each node from sum. If the value is 0 at any leaf, return true.

#### Complexity

O(NlogN) : ?

#### Cpp

- Solution.cpp

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
class Solution {
public:
    bool hasPathSum(TreeNode* node, int sum) {
        if(node == nullptr)
            return false;
        if(node->left == nullptr && node->right == nullptr && sum - node->val == 0){
            return true;
        }
        return hasPathSum(node->left, sum-node->val) || hasPathSum(node->right, sum-node->val);
    }
};
```
