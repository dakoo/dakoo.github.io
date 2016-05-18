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

```
def AddLevelItemsRecursively(two_dim_list, node, level):
	if node is None:
		return	
	if len(two_dim_list) != level:
		newlist = list()
		two_dim_list.append(newlist)
	two_dim_list[level-1].append(node.val)
	function(two_dim_list, node->left, level + 1)
	function(two_dim_list, node->right, level + 1)

def levelOrderBottom(root):
	two_dim_list = list()
	AddLevelItemsRecursively(two_dim_list, root, 1)
	two_dim_list.reverse()
	return two_dim_list
```

#### Complexity

Time complexity: O(N) 

#### Cpp

- Solution.hpp

```

```

- Solution.cpp

```cpp

```

