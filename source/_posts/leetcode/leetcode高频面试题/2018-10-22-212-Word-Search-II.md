---
title: 212. Word Search II
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-22 19:40:40
subtitle:
tags:
  - LeetCode
  - Trie
  - Recursion
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/word-search-ii/

### 题目描述

​	Given an `m x n` `board` of characters and a list of strings `words`, return *all words on the board*.

Each word must be constructed from letters of sequentially adjacent cells, where **adjacent cells** are horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/07/search1.jpg)

```
Input: board = [["o","a","a","n"],["e","t","a","e"],["i","h","k","r"],["i","f","l","v"]], words = ["oath","pea","eat","rain"]
Output: ["eat","oath"]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/07/search2.jpg)

```
Input: board = [["a","b"],["c","d"]], words = ["abcb"]
Output: []
```

 

**Constraints:**

- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 12`
- `board[i][j]` is a lowercase English letter.
- `1 <= words.length <= 3 * 104`
- `1 <= words[i].length <= 10`
- `words[i]` consists of lowercase English letters.
- All the strings of `words` are unique.

### 题目解析

- 这个和word Search 一的区别是，一只是给了一个单词，去判定存不存在，直接暴力搜就行，现在是判断一堆单词 看看拿个存在，也就是暴力搜的时候，你这个字符不仅仅是和一个单词的字符去比了，你要看，当前路径形成的单词有没有这个单词啊？是不是某个单词的前缀啊？要是直接比那就麻烦了，需要用有序表遍历挨个比，所以应该想到什么，Trie！它去组织单词，给组织起来，当前位置是单词就是，如果是路径也可以继续走，如果不是则直接返回。

### 代码

```java
class Solution {
   public static int[] dir_x = new int[]{-1,0,1,0};
    public static int[] dir_y = new int[]{0,1,0,-1};
    public static class PrefixTree{
        Node root;
        public class Node{
            Node[] nodes;
            boolean isWord;
            public Node() {
                nodes = new Node[26];
            }
        }

        public PrefixTree() {
            root = new Node();
        }
        public void insert(String word) {
            final char[] chars = word.toCharArray();
            Node cur = root;
            for (int i = 0; i < chars.length; i++) {
                int index = chars[i]-'a';
                cur.nodes[index] = cur.nodes[index] == null ? new Node():cur.nodes[index];
                cur = cur.nodes[index];
            }
            cur.isWord = true;
        }
    }
    public static List<String> findWords(char[][] board, String[] words) {
        final PrefixTree prefixTree = new PrefixTree();
        List<String> res = new ArrayList<>();
        final char[] path = new char[100];
        for (int i = 0; i < words.length; i++) {
            prefixTree.insert(words[i]);
        }
        for (int i = 0; i < board.length; i++) {
            for (int j = 0;j < board[0].length;j++){
                process(res,path,0,i,j,prefixTree.root,board);
            }
        }
        return res;
    }
    public static void process(List<String> res,char[] path,int k,int i,int j,PrefixTree.Node cur,char[][] board){
        if(i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] == '-')
            return;
        int index = board[i][j] - 'a';
        if(cur.nodes[index] == null) return;
         path[k] = board[i][j];
         board[i][j] = '-';
        if (cur.nodes[index].isWord) {
            res.add(new String(path,0,k+1));
            cur.nodes[index].isWord = false;
        }
        //继续以感染的方式 继续检测单词
        for (int d = 0;d < 4;d++){
            process(res,path,k+1,i+dir_x[d],j+dir_y[d],cur.nodes[index],board);
        }
        board[i][j] = path[k];
    }
}
```

