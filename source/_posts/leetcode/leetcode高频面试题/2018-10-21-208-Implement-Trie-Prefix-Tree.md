---
title: 208. Implement Trie (Prefix Tree)
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-21 19:31:17
subtitle:
tags:
  - Algorithm
  - Trie
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/implement-trie-prefix-tree/

### 题目描述

​	A [**trie**](https://en.wikipedia.org/wiki/Trie) (pronounced as "try") or **prefix tree** is a tree data structure used to efficiently store and retrieve keys in a dataset of strings. There are various applications of this data structure, such as autocomplete and spellchecker.

Implement the Trie class:

- `Trie()` Initializes the trie object.
- `void insert(String word)` Inserts the string `word` into the trie.
- `boolean search(String word)` Returns `true` if the string `word` is in the trie (i.e., was inserted before), and `false` otherwise.
- `boolean startsWith(String prefix)` Returns `true` if there is a previously inserted string `word` that has the prefix `prefix`, and `false` otherwise.

 

**Example 1:**

```
Input
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
Output
[null, null, true, false, true, null, true]

Explanation
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // return True
trie.search("app");     // return False
trie.startsWith("app"); // return True
trie.insert("app");
trie.search("app");     // return True
```

 

**Constraints:**

- `1 <= word.length, prefix.length <= 2000`
- `word` and `prefix` consist only of lowercase English letters.
- At most `3 * 104` calls **in total** will be made to `insert`, `search`, and `startsWith`.

### 题目解析

- 这个没什么难度，让实现一个Trie，只要学过这个东西，不难的。

### 代码

```java
class Trie {
    private class Node{
        Node[] nodes;
        boolean isWord;
          public Node() {
            nodes = new Node[26];
        }
    }
    Node root;

    public Trie() {
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

    public boolean search(String word) {
        final char[] chars = word.toCharArray();
        Node cur = root;
        for (int i = 0; i < chars.length; i++) {
            int index = chars[i]-'a';
            if(cur.nodes[index] != null)
                cur = cur.nodes[index];
            else
                return false;
        }
        return cur.isWord;
    }

    public boolean startsWith(String prefix) {
        final char[] chars = prefix.toCharArray();
        Node cur = root;
        for (int i = 0; i < chars.length; i++) {
            int index = chars[i]-'a';
            if(cur.nodes[index] != null)
                cur = cur.nodes[index];
            else
                return false;
        }
        return true;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

