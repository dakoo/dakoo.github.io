---
layout: post
title: leetcode-House Robber III
description: leetcode
modified: 2016-06-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - House Robber III](https://leetcode.com/problems/house-robber-iii/)

### Problem

While counting values of nodes, don't count directly-linked nodes. That is, we should avoid parent and child node.


If a node is selected, the children cann't be selected as follows.

```
    (3)
    / \
   2   3
    \   \ 
    (3) (1)
```
- 3 + 3 + 1

```
     3
    / \
  (4) (5)
  / \   \ 
 1   3   1
```

- 4 + 5

How about this?


```
     1
    / \
  (4)  5
  / \   \ 
 1   1  (100)
```

### Solution 

#### Approach

##### Recursion 

At each node, we should consider the maximum of the value of a tree which the node is the root. 
If the parent node is already selected, the children nodes cann't be selected. 
Otherwise, the parent is not selected, the children nodes have two options. One is "Selected" or "Not selected". The algorithm can be as follows:

```python
def maxValueOfTree(node, isParentNodeSelected):
    if node is None:
        return 0
    #The max value if the Parent node was selected, and this node cann't be selected
    if isParentNodeSelected == True:
        return maxValueOfTree(node.left, False) + maxValueOfTree(node.right, False)
    #The max value when the this node is NOT selected, regardless of the selection of the parent node.
    else: 
        notSelected = maxValueOfTree(node.left, False) + maxValueOfTree(node.right, False)
        selected = node.val + maxValueOfTree(node.left, True) + maxValueOfTree(node.right, True) 
        if notSelected > selected: 
            return notSelected
        return selected 
```

##### Think one step further 

Let's think about how to delete "isParentNodeSelected" argument because it increases the complexity of the implementation. We can make the function above as the following function.

```python
def MaxValueOfTree(node):
    if node is None:
        return 0

    #For Selected case
    selected = node.val
    if root.left is not None:
        selected = selected + MaxValueOfTree(node.left.left) + maxValueOfTree(node.left.right)
    if root.right is not None:
        selected = selected + MaxValueOfTree(node.right.left) + maxValueOfTree(node.right.right)

    #For not selected case
    notSelected = MaxValueOfTree(node.left) + MaxValueOfTree(node.right)

    return max([selected, notSelected])
```

##### Memoization 

We can apply memoization to the approach above. To implement memorization, let's use a hashmap. 
The key of the hashmap is 'TreeNode *' and the value is the return value.

```python
def MaxValueOfTree(self, node):
    if node is None:
        return 0

    # lookup the node in hashmap 
    val = self.table.get(node, -1);
    if val != -1:
        return val

    #For Selected case
    selected = node.val
    if root.left is not None:
        selected = selected + MaxValueOfTree(node.left.left) + maxValueOfTree(node.left.right)
    if root.right is not None:
        selected = selected + MaxValueOfTree(node.right.left) + maxValueOfTree(node.right.right)

    #For not selected case
    notSelected = MaxValueOfTree(node.left) + MaxValueOfTree(node.right)


    max_val = max([selected, notSelected])
    # update hashmap 
    self.table[node] = max_val

    return max_val
```

##### Complexity

Time complexity: O(N) thanks to memoization

#### C++

- Solution.cpp

```cpp
#include <unordered_map>
#include <algorithm>

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
private:
    std::unordered_map<TreeNode *, int> Table;
    int maxValueOfTree(TreeNode* node){
        if(node == nullptr)
            return 0;
        auto item = Table.find(node);
        if(item != Table.end()){
            return item->second;
        }
        int selected = node->val;        
        if(node->left){
            selected += maxValueOfTree(node->left->left) + maxValueOfTree(node->left->right);
        }
        if(node->right){
            selected += maxValueOfTree(node->right->left) + maxValueOfTree(node->right->right);
        }

        int notSelected = maxValueOfTree(node->left) + maxValueOfTree(node->right);
        int max_val = std::max(selected, notSelected);
        Table.insert({node, max_val});
        return max_val;
    }
public:
    int rob(TreeNode* root) {
        return maxValueOfTree(root);
    }
};

```
