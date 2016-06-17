---
layout: post
title: leetcode-Kth Smallest Element in a BST
description: leetcode
modified: 2016-06-16
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

### Problem

Implement a function to find out the kth smallest in a BST. 

### Solution 

#### Approach

##### Intuative approach

If you are considering recursive in-order traversal or DFS, it is a nice approach. However, you should also consider how they can be implemented.   
With recursive in-order traversal, how will you count and stop the process? With DFS, how will you check if a node is visited or not? It is not easy. 

##### Better approach

On each node, if you can count the number of nodes in the left child and right child tree respectively, you can find out the kth node very easily. 

1. Check the number of nodes in the child left on a node.
2. If the number of the left child tree on a node is K - 1, return the value of the node.
3. If the number of the left child tree on a node is bigger than K - 1, move to the left child and repeat 1 and 2. 
4. If the number of the left child tree on a node is smaller than K - 1, move to the right child and repeat 1 and 2. 

- Use the following algorithm to get the number of nodes in the left child tree

```
def getNumberOfNodes(node):
    if node is None:
        return 0
    return 1 + getNumberOfNodes(node.left) + getNumberOfNodes(node.right)
```

##### Complexity

Time complexity: O(N**2) (Worst), O(NlogN) (Average)

- Maximum depth of the tree: N (Worst), logN(Average)
- Time complexity to get the number of nodes in the left child tree: O(N)


### Implementation

#### C++

- Solution.cpp

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

class Solution {
private:
    int getNumberOfNodes(TreeNode* node) {
        if (node == nullptr)
            return 0;
        return 1 + getNumberOfNodes(node->left) + getNumberOfNodes(node->right);
    }
public:
    int kthSmallest(TreeNode* root, int k) {
        if (root == nullptr)
            return -1;          //The tree is empty
        TreeNode* node = root;
        while (node) {
            auto num_items_in_left_tree = getNumberOfNodes(node->left);
            if (num_items_in_left_tree + 1 == k) {
                return node->val;
            }
            else if (num_items_in_left_tree + 1 > k) {
                node = node->left;
            }
            else {
                k -= (num_items_in_left_tree + 1);
                node = node->right;
            }
        }
        return -1;              //the size of the tree is smaller than k
    }
};

```
