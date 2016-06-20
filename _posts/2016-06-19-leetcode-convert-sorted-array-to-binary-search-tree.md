---
layout: post
title: leetcode-Convert Sorted Array to BST
description: leetcode
modified: 2016-06-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Convert Sorted Array to Binary Search Tree](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)

### Problem

Converting a sorted array to a binary search tree. You should understand the characteristic of the BST. 

### Solution 

#### Approach

If you put the elements in a sorted array to a BST, the tree will become a skewed tree. For example, an array [1, 2, 3, 4, 5] will become as below.

```
           1
             \ 
              2 
               \ 
                3
                  \
                   4
                    \
                     5
```

You should insert the mid item in the array first. In this case, you should take 3 first. Therefore we can design the following algorithm.

1. Take the item in the middle of an array to make a node (The index of the item: (mid = low + high)/2)
2. You can split the array into two arrays. low ~ (mid - 1) for the left of the node; and  (mid+1)~ high for the right of the node.  
3. Repeat 1 and 2 until low is smaller than high in a recursive way

```
def pushItemInArrayToBST(nums, low, high):
  #if low == high, at least one node should be added to the BST
  if low > high:
    return None
  mid = (low + high) / 2
  node = TreeNode(nums[mid])
  node.left = pushItemInArrayToBST(nums, low, mid  - 1)
  node.right = pushItemInArrayToBST(nums, mid + 1, high)
  return node
  
def sortedArrayToBST(nums):
  if len(nums) == 0:
    return None
  return pushItemInArrayToBST(nums, 0, len(nums) - 1)
```

##### Complexity

- Time complexity: O(N)

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
	TreeNode* pushItemInArrayToBST(std::vector<int>& nums, int low_index, int high_index){
		if(low_index > high_index)
			return 0;
		int mid_index = (low_index + high_index)/2;
		TreeNode* node = new TreeNode(nums[mid_index]);
		node->left = pushItemInArrayToBST(nums, low_index, mid_index - 1);
		node->right = pushItemInArrayToBST(nums, mid_index + 1, high_index);
		return node;
	}
public:
    TreeNode* sortedArrayToBST(std::vector<int>& nums) {
		if(nums.size() == 0)
			return nullptr; 
		return pushItemInArrayToBST(nums, 0, nums.size()-1);
    }
};
```
