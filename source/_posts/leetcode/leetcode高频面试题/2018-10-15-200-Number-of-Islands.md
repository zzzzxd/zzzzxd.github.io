---
title: 200. Number of Islands
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-15 18:56:03
subtitle:
tags:
  - Union Find
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/number-of-islands/

### 题目描述

​	Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the number of islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

 

**Example 1:**

```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Example 2:**

```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

 

**Constraints:**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` is `'0'` or `'1'`.

### 题目解析

- 经典的岛屿问题，数有多少个岛屿，一般两种做法，一个是感染的思路，遍历矩阵，对每个没遍历过的，从它开始进行BFS感染，把整个联通块串起来，并且标记好访问过，那么 循环矩阵时，进行过几次BFS，就是几个块。
- 典型的并查集，并查集的作用就是来判断两个元素是不是在同一个集合，进行集合的合并，同时可以记录有多少个集合，每个集合有多少个元素等。我们对矩阵中的元素，对每一个都看它的右面和下面，进行集合的合并。

### 代码

```java
class Solution {
    private static  int[] parents;
    private static  boolean[] root; //是不是根
    public static int find(int c){
        int parent = c;
        while (parents[parent] != parent){
            parent = parents[parent];
        }
        //路经压缩
        parents[c] = parent;
        return parent;
    }
    public static void union(int a,int b){
        int root_a = find(a);
        int root_b = find(b);
        if(root_a != root_b){
            parents[root_a] = root_b;
            root[root_a] = false;
        }
    }
    public static int numIslands(char[][] grid) {
        if(grid.length <= 0) return 0;
        //初始化
        parents = new int[grid.length*grid[0].length];
        root = new boolean[grid.length*grid[0].length];
        for (int i = 0; i < parents.length; i++) {
            parents[i] = i;
        }
        for(int i  =0 ;i < grid.length;i++){
            for (int j = 0;j < grid[0].length;j++){
                if(grid[i][j] == '1'){
                    root[i*grid[0].length+j] = true;
                }
            }
        }
        for(int i = 1;i < grid[0].length;i++){
            if(grid[0][i] == '1' && grid[0][i-1] =='1'){
                union(i-1,i);
            }
        }
        for (int i = 1; i < grid.length; i++) {
            for(int j = 0;j < grid[0].length;j++){
                if(grid[i][j] == '1'){
                    //看左面
                    if(j != 0 && grid[i][j-1] == '1'){
                        union(i*grid[0].length+j-1,i*grid[0].length+j);
                    }
                    if(grid[i-1][j] == '1'){
                        union((i-1)*grid[0].length+j,i*grid[0].length+j);
                    }
                }
            }
        }
        int c = 0;
        for (int i = 0; i < root.length; i++) {
            if (root[i]) c++;
        }
        return c;
    }

}
```

