---
title: 102. Binary Tree Level Order Traversal
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-18 19:10:26
subtitle:
tags:
  - Tree
  - Recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/binary-tree-level-order-traversal/

### 题目描述

​	Given the `root` of a binary tree, return *the level order traversal of its nodes' values*. (i.e., from left to right, level by level).

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg)

```
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]
```

**Example 2:**

```
Input: root = [1]
Output: [[1]]
```

**Example 3:**

```
Input: root = []
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

### 题目解析

- 二叉树的层序遍历，常规操作.

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
     public List<List<Integer>> levelOrder(TreeNode root) {
        final List<List<Integer>> res = new ArrayList<>();
        if(root == null) return res;
        final Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        int batch = 1;
        while (!queue.isEmpty()){
            int next_batch=0;
            final ArrayList<Integer> list = new ArrayList<>();
            for (int i = 0;i < batch;i++){
                final TreeNode tmp = queue.poll();
                list.add(tmp.val);
                if (tmp.left != null){
                    queue.add(tmp.left);
                    next_batch++;
                } 
                if (tmp.right != null){
                    queue.add(tmp.right);
                    next_batch++;
                }
            }
            res.add(list);
            batch = next_batch;
        }
        return res;
    }
}
```

