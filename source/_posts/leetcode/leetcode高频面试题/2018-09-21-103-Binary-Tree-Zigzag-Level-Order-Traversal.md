---
title: 103. Binary Tree Zigzag Level Order Traversal
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-21 19:12:39
subtitle:
tags:
  - Tree
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/

### 题目描述

​	Given the `root` of a binary tree, return *the zigzag level order traversal of its nodes' values*. (i.e., from left to right, then right to left for the next level and alternate between).

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg)

```
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[20,9],[15,7]]
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
- `-100 <= Node.val <= 100`

### 题目解析

- 就是让你一层是从左到右，下一层又是从右到左
- 这里介绍一个特别重要的技巧，好多题目可以这么解，就是用队列层序遍历时候，每次从队列里拿元素，一次性拿一批，把这一层的都是拿出来，拿出来你想正着就正着，想倒着就倒着 再折腾一次就可以！

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
   public static List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        final List<List<Integer>> list = new ArrayList<>();
        if (root == null) return list;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        int batchSize = 1;
        int flag = 1;
       
        while (!queue.isEmpty()){
            final ArrayList<Integer> c = new ArrayList<>();
            int next_batch = 0;
            for(int i = 0;i < batchSize;i++){
                final TreeNode node = queue.poll();
                if(node.left != null){
                        queue.add(node.left);
                        next_batch++;
                }
                if(node.right != null){
                        queue.add(node.right);
                        next_batch++;
                }
                c.add(node.val);
            }
            if(flag == 0){
                    reverse(c);
                }
            batchSize = next_batch;
            flag ^= 1;
            list.add(c);
        }
        return list;
    }
    private static void reverse(ArrayList<Integer> c) {
        int len = c.size();
        for (int i = 0; i < c.size()/2; i++) {
            int tmp = c.get(i);
            c.set(i,c.get(len-1-i));
            c.set(len-1-i,tmp);
        }
    }
}
```

