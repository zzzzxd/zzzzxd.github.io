---
title: 94. Binary Tree Inorder Traversal
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-13 18:19:39
subtitle:
tags:
  - Recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/binary-tree-inorder-traversal/

### 题目描述

​	Given the `root` of a binary tree, return *the inorder traversal of its nodes' values*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```
Input: root = [1,null,2,3]
Output: [1,3,2]
```

**Example 2:**

```
Input: root = []
Output: []
```

**Example 3:**

```
Input: root = [1]
Output: [1]
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 100]`.
- `-100 <= Node.val <= 100`



### 题目解析

- 普通的中序遍历

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
     public static List<Integer> inorderTraversal(TreeNode root) {
        final ArrayList<Integer> res = new ArrayList<>();
        process(res,root);
        return res;
    }
    public static void process(List<Integer> res,TreeNode root){
          if(root == null) return;
          process(res,root.left);
          res.add(root.val);
          process(res,root.right);
    }
}
```

