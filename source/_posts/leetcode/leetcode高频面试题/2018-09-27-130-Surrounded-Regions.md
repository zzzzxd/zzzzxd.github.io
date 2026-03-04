---
title: 130. Surrounded Regions
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-09-27 07:01:04
subtitle:
tags:
  -	BFS
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/surrounded-regions/

### 题目描述

​	Given an `m x n` matrix `board` containing `'X'` and `'O'`, *capture all regions that are 4-directionally surrounded by* `'X'`.

A region is **captured** by flipping all `'O'`s into `'X'`s in that surrounded region.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/xogrid.jpg)

```
Input: board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
Explanation: Notice that an 'O' should not be flipped if:
- It is on the border, or
- It is adjacent to an 'O' that should not be flipped.
The bottom 'O' is on the border, so it is not flipped.
The other three 'O' form a surrounded region, so they are flipped.
```

**Example 2:**

```
Input: board = [["X"]]
Output: [["X"]]
```

 

**Constraints:**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 200`
- `board[i][j]` is `'X'` or `'O'`.

### 题目解析

- 这个题说的是，把完全包起来的区域里的O->X,边界上能联通的O不要变，这个还是挺好的，要求你转换思路，我们先把O存成另一个字符C，然后从边界出发去进行BFS 也就是 感染操作，上下左右的去联通，联通了就置成另一个字符K，那么我们再进行遍历，所有的C变成X，所有的K变回O即可

### 代码

```java
class Solution {
     static int M,N;
    public static int[] dir_x = new int[]{-1,0,1,0};
    public static int[] dir_y = new int[]{0,1,0,-1};
    
    public static void solve(char[][] board) {
        M = board.length;
        N = board[0].length;
        for (int i = 0;i < M;i++){
            //统一表示成'C'
            for (int j = 0;j < N;j++){
                    if ( board[i][j] == 'O')
                         board[i][j] = 'C';
            }
        }
        //开始从边界进行扫描
        for (int i = 0;i < N;i++) {
            infect(board,0,i);
            infect(board,M-1,i);
        }
        for(int i = 0;i < M;i++){
            infect(board,i,0);
            infect(board,i,N-1);
        }
        for (int i = 0;i < M;i++){
            //统一表示成'C'
            for (int j = 0;j < N;j++){
                if ( board[i][j] == 'C')
                    board[i][j] = 'X';
                if ( board[i][j] == 'K')
                    board[i][j] = 'O';
            }
        }
    }
    public static void infect(char[][] board,int i,int j){
        if(isJudge(i,j) && board[i][j] == 'C'){
            board[i][j] = 'K';
            for (int d = 0;d < 4;d++){
                infect(board,i+dir_x[d],j + dir_y[d]);
            }
        }
    }
    public static  boolean isJudge(int i,int j){
        return i >= 0 && i < M && j >= 0&& j<N;
    }

}
```

