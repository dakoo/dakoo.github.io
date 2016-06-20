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

1. Take the item in the middle of an array. (The index of the item: (mid = low + high)/2)
2. You can split the array into two arrays. The items smaller than the item above and the items bigger than the item. (low ~ (mid - 1),  (mid+1)~high )
3. Repeat 1 and 2 until low is smaller than high.

```
//
```

##### Complexity

-

#### C++

- Solution.cpp

```cpp
//
```
