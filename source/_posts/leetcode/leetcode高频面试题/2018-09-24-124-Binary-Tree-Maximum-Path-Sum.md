---
title: 124. Binary Tree Maximum Path Sum
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-24 23:57:30
subtitle:
  - Tree
  - Recursion
categories: 
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/binary-tree-maximum-path-sum/submissions/

### 题目描述

​	A **path** in a binary tree is a sequence of nodes where each pair of adjacent nodes in the sequence has an edge connecting them. A node can only appear in the sequence **at most once**. Note that the path does not need to pass through the root.

The **path sum** of a path is the sum of the node's values in the path.

Given the `root` of a binary tree, return *the maximum **path sum** of any **non-empty** path*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/13/exx1.jpg)

```
Input: root = [1,2,3]
Output: 6
Explanation: The optimal path is 2 -> 1 -> 3 with a path sum of 2 + 1 + 3 = 6.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/10/13/exx2.jpg)

```
Input: root = [-10,9,20,null,null,15,7]
Output: 42
Explanation: The optimal path is 15 -> 20 -> 7 with a path sum of 15 + 20 + 7 = 42.
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 3 * 104]`.
- `-1000 <= Node.val <= 1000`

### 题目解析

- 二叉树的递归套路，主要靠一点区分就是，这个最大路径要么经过我root，要么不经过，如果经过的话，一定要和子树经过头的最大路径连起来才可以。

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
    public static class NodeInfo{
        int maxSum;
        int fromHeadMaxSum;

        public NodeInfo(int maxSum, int fromHeadMaxSum) {
            this.maxSum = maxSum;
            this.fromHeadMaxSum = fromHeadMaxSum;
        }
    }

    public static  int maxPathSum(TreeNode root) {
        return maxPathSumProcess(root).maxSum;
    }
    public static NodeInfo maxPathSumProcess(TreeNode root) {
        if (root == null) return null;
        int maxSum = root.val;
        int fromHeadMaxSum = root.val;
        final NodeInfo leftInfo = maxPathSumProcess(root.left);
        final NodeInfo rightInfo = maxPathSumProcess(root.right);
        //第一种情况，不经过root
        if(leftInfo != null){
            maxSum = Math.max(leftInfo.maxSum,maxSum);
        }
        if (rightInfo != null){
            maxSum = Math.max(rightInfo.maxSum,maxSum);
        }
        //第二种，经过root了
        if (leftInfo!=null && leftInfo.fromHeadMaxSum > 0){
            maxSum = Math.max(maxSum, leftInfo.fromHeadMaxSum + root.val);
            fromHeadMaxSum = Math.max(leftInfo.fromHeadMaxSum + root.val,fromHeadMaxSum);
        }
        if(rightInfo!=null && rightInfo.fromHeadMaxSum > 0){
            maxSum = Math.max(maxSum, rightInfo.fromHeadMaxSum + root.val);
            fromHeadMaxSum = Math.max(rightInfo.fromHeadMaxSum + root.val,fromHeadMaxSum);
        }
        if (leftInfo != null && rightInfo != null && leftInfo.fromHeadMaxSum > 0 && rightInfo.fromHeadMaxSum > 0){
            maxSum = Math.max(maxSum, leftInfo.fromHeadMaxSum + root.val + rightInfo.fromHeadMaxSum);
        }
        return new NodeInfo(maxSum,fromHeadMaxSum);
    }
}
```



