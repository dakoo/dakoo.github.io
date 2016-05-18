---
layout: post
title: leetcode-Binary Tree Level Order Traversal II
description: leetcode
modified: 2016-05-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Binary Tree Level Order Traversal II](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)

### Problem

Manipulating a binary tree. 
In this problem, the most difficult part is that you should return two dimensional arrays. 

### Solution 

#### Approach


Normal BFS or Queue/Stack combination can't make the dimesional arrays because they don't notice when level is changed.
Therefore, consider more straightforward solutions using recusion with level information as follows. 

```python
def AddLevelItemsRecursively(two_dim_list, node, level):
	if node is None:
		return	
	if len(two_dim_list) == level - 1:
		newlist = list()
		two_dim_list.append(newlist)
	two_dim_list[level-1].append(node.val)
	AddLevelItemsRecursively(two_dim_list, node.left, level + 1)
	AddLevelItemsRecursively(two_dim_list, node.right, level + 1)

def levelOrderBottom(root):
	two_dim_list = list()
	AddLevelItemsRecursively(two_dim_list, root, 1)
	two_dim_list.reverse()
	return two_dim_list
```

#### Complexity

Time complexity: O(N) 

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
class Solution {
private:
    void _addLevelItemsRecursively(vector<vector<int> >& llist, TreeNode* node, int level);
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"

void Solution::_addLevelItemsRecursively(vector<vector<int> >& llist, TreeNode* node, int level) {
    if(node == nullptr)
        return;
    if(llist.size() == level - 1){
        vector<int> v;
        llist.push_back(v);
    }
    llist[level - 1].push_back(node->val);
    _addLevelItemsRecursively(llist, node->left, level+1);
    _addLevelItemsRecursively(llist, node->right, level+1);
}
vector<vector<int>> Solution::levelOrderBottom(TreeNode* root) {
    vector<vector<int> > llist;
    _addLevelItemsRecursively(llist, root, 1);
    reverse(begin(llist), end(llist)); 
    return llist;
}
```

