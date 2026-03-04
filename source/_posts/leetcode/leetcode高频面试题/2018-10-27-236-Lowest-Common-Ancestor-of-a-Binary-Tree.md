---
title: 236. Lowest Common Ancestor of a Binary Tree
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-27 23:39:43
subtitle:
tags:
  - LeetCode
  - Tree 
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

### 题目描述

​	Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
```

**Example 3:**

```
Input: root = [1,2], p = 1, q = 2
Output: 1
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[2, 105]`.
- `-109 <= Node.val <= 109`
- All `Node.val` are **unique**.
- `p != q`
- `p` and `q` will exist in the tree.

### 题目解析

- 最近公共祖先问题 LCA问题，也是经典的问题，简单的做法就是 求出跟几点到这个节点的路径，两条路径拿出来，再找第一个相交节点即可
- 高级的做法是树链剖分，这个数据结构比较复杂，专门出一章来解释，现在先用简单的做法。

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
          final ArrayList<TreeNode> path1 = new ArrayList<>();
          final ArrayList<TreeNode> path2 = new ArrayList<>();
          findX(path1,root,p.val);
          findX(path2,root,q.val);
          int i = path1.size()-1,j = path2.size() -1;
          for (int k = 0;k < path1.size() - path2.size();k++) i--;
          for (int k = 0;k < path2.size() - path1.size();k++) j--;
              

          for (;i >=0;i--,j--) if (path1.get(i) == path2.get(j)) return path1.get(i);
          return null;
      }
      public Integer findX(List<TreeNode> path,TreeNode root,int t){
          if(root == null) return -1;
          path.add(root);
          if (root.val == t) return 1;
          if(findX(path,root.left,t)==1) return 1;
          if(findX(path,root.right,t) == 1) return 1;
          path.remove(path.size()-1);
          return -1;
      }
}
```

