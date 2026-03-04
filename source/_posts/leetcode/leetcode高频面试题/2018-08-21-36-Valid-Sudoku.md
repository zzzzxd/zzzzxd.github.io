---
title: 36. Valid Sudoku
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-08-21 17:31:14
subtitle:
tags:
  - Array
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

https://leetcode.com/problems/valid-sudoku/

### 题目描述

Determine if a `9 x 9` Sudoku board is valid. Only the filled cells need to be validated **according to the following rules**:

1. Each row must contain the digits `1-9` without repetition.
2. Each column must contain the digits `1-9` without repetition.
3. Each of the nine `3 x 3` sub-boxes of the grid must contain the digits `1-9` without repetition.

**Note:**

- A Sudoku board (partially filled) could be valid but is not necessarily solvable.
- Only the filled cells need to be validated according to the mentioned rules.

 

**Example 1:**

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

```
Input: board = 
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: true
```

**Example 2:**

```
Input: board = 
[["8","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: false
Explanation: Same as Example 1, except with the 5 in the top left corner being modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.
```

 

**Constraints:**

- `board.length == 9`
- `board[i].length == 9`
- `board[i][j]` is a digit `1-9` or `'.'`.

### 题目解析

- 其实就是遍历，判断一下此时在列方向、行方向是不是不满足条件，遍历过程中标记好条件即可。

### 代码

```java
class Solution {
  public boolean isValidSudoku(char[][] board) {
        //主要是存当前行、当前列、当前子块
        int[][] rowVisited = new int[9][10]; // 第二维是数字
        int[][] colVisited = new int[9][10]; //第二维是数字
        int[][] subMatrixVisited = new int[9][10];
        for (int i = 0;i < 9;i++){
            for (int j = 0;j < 9;j++){
                    int subId =3 * (i/3) + j/3;
                    char t = board[i][j]; 
                    if(t == '.')
                        continue;
                    int num = t - '0';
                    if(rowVisited[i][num] == 1 || colVisited[j][num] == 1 || subMatrixVisited[subId][num]==1){
                        return false;
                    }
                    rowVisited[i][num] = 1;
                    colVisited[j][num] = 1;
                    subMatrixVisited[subId][num]=1;
                }
            }
        return true;
        }
}
```

