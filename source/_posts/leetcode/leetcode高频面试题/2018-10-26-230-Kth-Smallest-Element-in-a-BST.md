---
title: 230. Kth Smallest Element in a BST
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-26 23:13:01
subtitle:
tags:
  - LeetCode
  - Tree
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/kth-smallest-element-in-a-bst/

### 题目描述

​	Given the `root` of a binary search tree, and an integer `k`, return *the* `kth` *smallest value (**1-indexed**) of all the values of the nodes in the tree*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/28/kthtree1.jpg)

```
Input: root = [3,1,4,null,2], k = 1
Output: 1
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/01/28/kthtree2.jpg)

```
Input: root = [5,3,6,2,4,null,null,1], k = 3
Output: 3
```

 

**Constraints:**

- The number of nodes in the tree is `n`.
- `1 <= k <= n <= 104`
- `0 <= Node.val <= 104`

### 题目解析

- BST中寻找第K小，其实就是中序遍历的第K个节点,直接递归时候找出来就行

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
   int c = 0;
    public int kthSmallest(TreeNode root, int k) {
        return process(root,k);
    }
    public  int process(TreeNode root,int k){
        if (root== null){
            return -1;
        }
        final int l = process(root.left, k);
        if (l != -1) return l;
        c++;
        if(c == k) return root.val;
        final int r = process(root.right, k);
        if (r != -1) return r;
        return -1;
    }

}
```

