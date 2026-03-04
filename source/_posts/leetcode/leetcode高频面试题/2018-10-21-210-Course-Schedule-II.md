---
title: 210. Course Schedule II
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2018-10-21 19:33:22
subtitle:
tags:
  - LeetCode
  - Topological Sorting
categories:
  - 算法
    - leetcode高频面试题
---

### 题目链接

​	https://leetcode.com/problems/course-schedule-ii/

### 题目描述

​	There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.

- For example, the pair `[0, 1]`, indicates that to take course `0` you have to first take course `1`.

Return *the ordering of courses you should take to finish all courses*. If there are many valid answers, return **any** of them. If it is impossible to finish all courses, return **an empty array**.

 

**Example 1:**

```
Input: numCourses = 2, prerequisites = [[1,0]]
Output: [0,1]
Explanation: There are a total of 2 courses to take. To take course 1 you should have finished course 0. So the correct course order is [0,1].
```

**Example 2:**

```
Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
Output: [0,2,1,3]
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0.
So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3].
```

**Example 3:**

```
Input: numCourses = 1, prerequisites = []
Output: [0]
```

 

**Constraints:**

- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= numCourses * (numCourses - 1)`
- `prerequisites[i].length == 2`
- `0 <= ai, bi < numCourses`
- `ai != bi`
- All the pairs `[ai, bi]` are **distinct**.

### 题目解析

- 和课程安排一 没啥区别。

### 代码

```java
class Solution {
     public static int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses == 1 ) return new int[]{0};
        int[] v_edge = new int[numCourses];
        final Map<Integer, List> adjacent = getAdjacent(prerequisites,v_edge);
        return judge(v_edge,adjacent);
    }
    public static Map<Integer, List> getAdjacent(int[][] prerequisites, int[] v_edge){
        final HashMap<Integer, List> adject = new HashMap<>();
        for (int i = 0; i < prerequisites.length; i++) {
            int from = prerequisites[i][1];
            int to = prerequisites[i][0];
            v_edge[to]++;
            if(adject.containsKey(from)){
                adject.get(from).add(to);
            }else{
                final List<Integer> list = new ArrayList<>();
                list.add(to);
                adject.put(from,list);
            }
        }
        return adject;
    }

    public static int[] judge(int[] v_edge,Map<Integer, List> map){
        final int[] collect = new int[v_edge.length];
        final Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < v_edge.length; i++) {
            if(v_edge[i] == 0) queue.add(i);
        }
        int res = 0;
        while (!queue.isEmpty()){
            final Integer v = queue.poll();
            collect[res++] = v;
            final List<Integer> list = map.get(v);
            if(list != null){
                for (int i = 0; i < list.size(); i++) {
                    Integer t = list.get(i);
                    if(--v_edge[t] == 0){
                        queue.add(t);
                    }
                }
            }
        }
        if(res == v_edge.length) return collect;
        else return new int[]{};
    }
}
```

