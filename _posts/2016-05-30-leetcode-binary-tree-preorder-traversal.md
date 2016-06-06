---
layout: post
title: leetcode-Binary Tree Preorder Traversal
description: leetcode
modified: 2016-05-25
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)

### Problem

Binary Tree Traversal in recursive and iterative. Recursive solution is easy. Rather, iterative solution might be difficult.

### Solution 

#### Approach

##### Recursive

- Trivial

```python
def preorder_travesal(node):
    if node is None: 
        return
    print node.value
    preorder_travesal(node.left)
    preorder_travesal(node.right)
```

##### Interative 

- Consider a stack or queue. To mimic the recursive preorder traversal, a stack might be better. 

```python
def preorder_travesal(root):
    if root is None:
        return None
    stack = list()
    stack.append(root)
    while len(stack) != 0:
        node = stack.pop()
        print node.val
        if node.right != None:
            stack.append(node.right)
        if node.left != None:
            stack.append(node.left)
```

#### Complexity

Time complexity: O(n)

#### Cpp

##### Recursive

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
    void _traversal(std::vector<int> &v, TreeNode* root);
public:
    std::vector<int> preorderTraversal(TreeNode* root);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

void Solution::_traversal(std::vector<int> &v, TreeNode* node){
    if(node == nullptr)    
        return;
    v.emplace_back(node->val);
    _traversal(v, node->left);
    _traversal(v, node->right);
}

std::vector<int> Solution::preorderTraversal(TreeNode* root) {
    vector<int> ret;
    _traversal(ret, root);
    return ret;
}

```

##### Iterative

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
public:
    std::vector<int> preorderTraversal(TreeNode* root);
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

std::vector<int> Solution::preorderTraversal(TreeNode* root) {
    std::vector<int> ret;
    if(root == nullptr)
        return ret;
    std::vector<TreeNode*> stack;
    stack.push_back(root);
    while(!stack.empty()){
        TreeNode* node = stack.back();
        stack.pop_back();
        ret.emplace_back(node->val); 
        if(node->right)
            stack.push_back(node->right);
        if(node->left)
            stack.push_back(node->left);
    }
    return ret;     
}

```
