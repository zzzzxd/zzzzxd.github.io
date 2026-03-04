---
title: 395. Longest Substring with At Least K Repeating Characters
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-11-11 23:52:40
subtitle:
tags:
  - LeetCode
  - Sliding Window
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/

### 题目描述

​	Given a string `s` and an integer `k`, return *the length of the longest substring of* `s` *such that the frequency of each character in this substring is greater than or equal to* `k`.

 

**Example 1:**

```
Input: s = "aaabb", k = 3
Output: 3
Explanation: The longest substring is "aaa", as 'a' is repeated 3 times.
```

**Example 2:**

```
Input: s = "ababbc", k = 2
Output: 5
Explanation: The longest substring is "ababb", as 'a' is repeated 2 times and 'b' is repeated 3 times.
```

 

**Constraints:**

- `1 <= s.length <= 104`
- `s` consists of only lowercase English letters.
- `1 <= k <= 105`

### 题目解析

- 乍一看 特像滑动窗口对吧，直接用，一会发现，窗口结束不了，比如说，L =  0，R取扫描，对于abbbcccdefg    k =2的情况，明明只要L=1，R = 6位置就能得到答案的，结果R = 6，不满足条件啊，因为a还不够2啊，所以R 继续扫，又引入了新的字符，永远满足不了条件！
- 这个窗口不能直接用，就是因为你会不断的引入新字符，你得给认为的中断！比如讲，每次用窗口，认为的规定字符种类就必须是2种，那么遇到c的时候 L必须前移！
- 好好体会这道 窗口不能直接用的题目！

### 代码

```java
class Solution {
   //TODO:做的还是慢
    public static int longestSubstring(String s, int k) {
        final char[] chars = s.toCharArray();
        int res = 0;
        final HashSet<Integer> set = new HashSet<>();
        for (int i = 1;i <= 26;i++){
           int p1 = 0,p2 = 0;
           int[] visited = new int[26];
           int kind = 0;
           while (p1 < chars.length){
               while (p2 < chars.length){
                   int expect = chars[p2]-'a';
                   if(visited[expect] == 0){
                       if(kind+1 > i)
                        break;
                       kind++;
                       set.add(expect);
                   }
                   visited[expect]++;
                   p2++;
               }
               if(kind == i){
                   final Iterator<Integer> iterator = set.iterator();
                   boolean flag = true;
                   while (iterator.hasNext()){
                       final Integer next = iterator.next();
                       if(visited[next] < k){
                           flag = false;
                       }
                   }
                   if (flag){
                       res = Math.max(res,p2-p1);
                   }
               }
               if(--visited[chars[p1]-'a'] == 0){
                   kind--;
                   set.remove(chars[p1]-'a');
               }
               p1++;
           }
        }
        return res;
    }

}
```

