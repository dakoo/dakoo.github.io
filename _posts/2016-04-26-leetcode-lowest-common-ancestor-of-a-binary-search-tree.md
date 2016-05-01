---
layout: post
title: leetcode-lowest common ancestor of a binary search tree
description: leetcode
modified: 2016-04-26
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - lowest common ancestor of a binary search tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

### Problem

One of Binary "Search" Tree problems

### Solution 

#### Approach

Think of the characteristics of BST. All items of the left child tree of a node have smaller value than the node.  All items of the right child tree of a node have bigger value than the node. Thus, you can figure out given two values are located in the left or right child tree of a node, if you compare the values with the node's value. If both of the values are smaller than the node's value, they are in the left child tree of the node. Otherwise, right child tree of the node. 

```

        _______6______
       /              \
    ___2__          ___8__
   /      \        /      \
   0      _4       7       9
         /  \
         3   5

```

As seen from the tree above, 

- Given 2 and 8 : 2 is smaller than 6 and 8 is larger than 8. Thus, on 6, you can figure out that 6 is the LCA of 2 and 8.   
- Given 2 and 4 : On 6, both of them is smaller than 6. So move to the left child 2. On 2, since 2 == 2, 2 is the LCA of 2 and 4.

#### Complexity

O(LogN) : Because the max depth of Binary Search Tree is LogN. 

#### Cpp

```
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
class Solution {
private:
    TreeNode* root;
    TreeNode* lowestCommonAncestor_iterative(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* temp = root;
        while((temp->val - p->val) * (temp->val - q->val) > 0){
            if(temp->val > p->val) 
                temp = temp->left;
            else 
                temp = temp->right;
        }
        return temp;
    };
    TreeNode* lowestCommonAncestor_recursive(TreeNode* node, TreeNode* p, TreeNode* q) {
        if((node->val - p->val) * (node->val - q->val) > 0){
            if(node->val > p->val) 
                return lowestCommonAncestor(node->left, p, q);
            else 
                return lowestCommonAncestor(node->right, p, q);
        } 
        return node;
    };
};
```

#### Java

RomanToIntConverter.java 

```java
public class Solution {
	public class TreeNode {
	    int val;
	    TreeNode left;
	    TreeNode right;
	    TreeNode(int x) { val = x; }
	}
    public TreeNode lowestCommonAncestor_iterative(TreeNode root, TreeNode p, TreeNode q) {
   		TreeNode temp = root; 
   		while((temp.val - p.val) * (temp.val - q.val) > 0){
   			if(temp.val > p.val)
   				temp = temp.left;
   			else
   				temp = temp.right;
   		}
   		return temp;
    }
    public TreeNode lowestCommonAncestor_recursive(TreeNode node, TreeNode p, TreeNode q) {
   		if( (node.val - p.val) * (node.val - q.val) > 0){
   			if(node.val > p.val)
   				return lowestCommonAncestor_recursive(node.left, p, q);
   			else 
   				return lowestCommonAncestor_recursive(node.right, p, q);
   		}  
   		return node;
    }
}
```

#### Python

```python
class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution(object):
    def lowestCommonAncestor_iterative(self, root, p, q):
    	temp = root
    	while (temp.val - p.val)*(temp.val - q.val) > 0 :
    		if temp.val > p.val :
    			temp = temp.left
    		else : 
    			temp = temp.righ
    	return temp
    def lowestCommonAncestor_recursive(self, node, p, q):
    	if (node.val - p.val)*(node.val - q.val) > 0 :
    		if node.val > p.val:
    			return self.lowestCommonAncestor_recursive(node.left, p, q)
    		else:
    			return self.lowestCommonAncestor_recursive(node.right, p, q)
    	else:
    		return node
```
