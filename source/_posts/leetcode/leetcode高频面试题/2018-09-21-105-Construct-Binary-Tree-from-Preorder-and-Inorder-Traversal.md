---
title: 105. Construct Binary Tree from Preorder and Inorder Traversal
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-21 19:27:13
subtitle:
tags:
  - Tree
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

### 题目描述

​	Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return *the binary tree*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

**Example 2:**

```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

 

**Constraints:**

- `1 <= preorder.length <= 3000`
- `inorder.length == preorder.length`
- `-3000 <= preorder[i], inorder[i] <= 3000`
- `preorder` and `inorder` consist of **unique** values.
- Each value of `inorder` also appears in `preorder`.
- `preorder` is **guaranteed** to be the preorder traversal of the tree.
- `inorder` is **guaranteed** to be the inorder traversal of the tree.

### 题目解析

- 前序遍历与中序遍历，前序与层序，加上保证值不同，便可以唯一确定一棵树，先序确定出跟，然后另一个数组确定出对应的左右子树。

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
  public TreeNode buildTree(int[] preorder, int[] inorder) {
        final HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i],i);
        }
        return constuctTree(preorder,0,preorder.length-1,0,inorder.length-1,map);
    }

    public TreeNode constuctTree(int[] preorder,int l1,int r1,int l2,int r2,HashMap<Integer,Integer> map){
            if(l1 > r1) return null;
            TreeNode root = new TreeNode(preorder[l1]);
            final Integer loc = map.get(root.val);
            final TreeNode left = constuctTree(preorder, l1 + 1, l1 + loc -l2, l2, loc - 1, map);
            final TreeNode right = constuctTree(preorder, l1 + loc - l2 + 1, l1 - l2 + r2, loc + 1, r2, map);
            root.left = left;
            root.right = right;
            return root;
    }
}
```

