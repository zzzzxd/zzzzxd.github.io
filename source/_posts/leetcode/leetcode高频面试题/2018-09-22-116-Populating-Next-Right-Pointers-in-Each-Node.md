---
title: 116. Populating Next Right Pointers in Each Node
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-22 19:39:06
subtitle:
tags:
  -	Tree
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/populating-next-right-pointers-in-each-node/

### 题目描述

​	You are given a **perfect binary tree** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

```
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```

**Example 2:**

```
Input: root = []
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 212 - 1]`.
- `-1000 <= Node.val <= 1000`

 

**Follow-up:**

- You may only use constant extra space.
- The recursive approach is fine. You may assume implicit stack space does not count as extra space for this problem.

### 题目解析

- T103 提到了层次遍历一次取一批的技巧，这个题就用的上了，一次取出来一批，设置好右节点即可 。

### 代码

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node next;

    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, Node _left, Node _right, Node _next) {
        val = _val;
        left = _left;
        right = _right;
        next = _next;
    }
};
*/

class Solution {
    public static Node connect(Node root) {
        if(root == null) return root;
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        int bathSize = 1;
        while (!queue.isEmpty()){
            Node pre = null;
            int next_batch = 0;
            for (int i = 0; i < bathSize; i++) {
                final Node cur = queue.poll();
                if(pre == null){
                    pre = cur;
                }else{
                    pre.next = cur;
                     pre = cur;
                }
                if(cur.left != null){
                    queue.add(cur.left);
                    next_batch++;
                }
                if (cur.right != null){
                    queue.add(cur.right);
                    next_batch++;
                }
            }
            bathSize = next_batch;
        }
        return root;
    }
}
```

