---
title: 297. Serialize and Deserialize Binary Tree
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-28 19:40:03
subtitle:
tags:
  - LeetCode
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/serialize-and-deserialize-binary-tree/

### 题目描述

​	Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

**Clarification:** The input/output format is the same as [how LeetCode serializes a binary tree](https://leetcode.com/faq/#binary-tree). You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/09/15/serdeser.jpg)

```
Input: root = [1,2,3,null,null,4,5]
Output: [1,2,3,null,null,4,5]
```

**Example 2:**

```
Input: root = []
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 104]`.
- `-1000 <= Node.val <= 1000`

### 题目解

- 树的序列化与反序列化，把null值用一个特殊符号代替即可。

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
public class Codec {
    public static int step = 0;
    public static  String serialize(TreeNode root) {
        if (root == null) return "";
        final StringBuilder builder = new StringBuilder();
        process(root,builder);
        final String str = builder.toString();
        return str.substring(0,str.length()-1); //去掉最后一个#
    }
    public static void process(TreeNode root,StringBuilder builder){
        if (root == null){
            builder.append("*#");
            return;
        }
        builder.append(root.val+"#");
        process(root.left,builder);
        process(root.right,builder);
    }
    // Decodes your encoded data to tree.
  
     public static TreeNode deserialize(String data) {
        if ("".equals(data)) return null;
        final String[] split = data.split("#");
        step = 0;
        return unprocess(split);
    }
    public static TreeNode unprocess(String[] strs){
        if (strs[step].equals("*")) {
            step++;
            return null;
        }
        else{
            final int num = Integer.parseInt(strs[step++]);
            final TreeNode cur = new TreeNode(num);
            cur.left = unprocess(strs);
            cur.right = unprocess(strs);
            return cur;
        }
    }
}

// Your Codec object will be instantiated and called as such:
// Codec ser = new Codec();
// Codec deser = new Codec();
// TreeNode ans = deser.deserialize(ser.serialize(root));
```

