---
title: 108. Convert Sorted Array to Binary Search Tree
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-22 19:35:45
subtitle:
tags:
  - Tree
  - Binary Search
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/

### 题目描述

​	Given an integer array `nums` where the elements are sorted in **ascending order**, convert *it to a **height-balanced** binary search tree*.

A **height-balanced** binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/18/btree1.jpg)

```
Input: nums = [-10,-3,0,5,9]
Output: [0,-3,9,-10,null,5]
Explanation: [0,-10,5,null,-3,null,9] is also accepted:
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/02/18/btree.jpg)

```
Input: nums = [1,3]
Output: [3,1]
Explanation: [1,null,3] and [3,1] are both height-balanced BSTs.
```

 

**Constraints:**

- `1 <= nums.length <= 104`
- `-104 <= nums[i] <= 104`
- `nums` is sorted in a **strictly increasing** order.

​	

### 题目解析

- 这是个简单题，主要要求的很明确了，要是高度是平衡的，那么就用二分，中点当根，然后递归下去即可。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
     public static TreeNode sortedArrayToBST(int[] nums) {
        return process(nums,0,nums.length-1);
    }
    public static TreeNode process(int[] num,int l,int r){
        if (l > r){
            return null;
        }
        int mid = l + (r -l)/2;
        final TreeNode left = process(num, l, mid - 1);
        final TreeNode right = process(num, mid+1, r);
        final TreeNode root = new TreeNode();
        root.left = left;
        root.right = right;
        root.val = num[mid];
        return root;
    }
}
```

