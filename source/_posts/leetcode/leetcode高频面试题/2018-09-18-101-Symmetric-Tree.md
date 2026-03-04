---
title: 101. Symmetric Tree
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-18 19:04:22
subtitle:
tags:
  - Recursion
  - Tree
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/symmetric-tree/	

### 题目描述

​	Given the `root` of a binary tree, *check whether it is a mirror of itself* (i.e., symmetric around its center).

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/symtree1.jpg)

```
Input: root = [1,2,2,3,4,4,3]
Output: true
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/02/19/symtree2.jpg)

```
Input: root = [1,2,2,null,3,null,3]
Output: false
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 1000]`.
- `-100 <= Node.val <= 100`

### 题目解析

- 好题啊好题，虽说是简单题，还蛮考验小聪明的，其实就是说你得始终验证每个节点的左树是不是和右树一样，对每个节点都得验证这是递归，进行验证的时候，你得拿到对应的兄弟节点，所以可以 直接传两次！把树直接传两次！

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
   
    public boolean isSymmetric(TreeNode root) {
        return isMirror(root,root);
    }

    public boolean isMirror(TreeNode leftRoot,TreeNode rightRoot){
        if(leftRoot != null && rightRoot != null){
            if(leftRoot.val != rightRoot.val) return false;
            else
                return isMirror(leftRoot.left,rightRoot.right) && isMirror(leftRoot.right,rightRoot.left);
        }
        else if(leftRoot == null && rightRoot == null){
            return true;
        }else
            return false;
    }
}
```

