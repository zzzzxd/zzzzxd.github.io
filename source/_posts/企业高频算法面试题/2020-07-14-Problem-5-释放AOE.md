---
title: Problem 5 释放AOE
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2020-07-14 10:38:11
subtitle:
tags:
  -	algorithm
  - Greed
categories:
  -	算法
    - 企业面试题
---

### 题目

给定两个非负数组x和hp，长度都是N，再给定一个正数range  ,x有序，x[i]表示i号怪兽在x轴上的位置；hp[i]表示i号怪兽的血量  再给定一个正数range，表示如果法师释放技能的范围长度 被打到的每只怪兽损失1点血量。
返回要把所有怪兽血量清空，至少需要释放多少次AOE技能？

### 题目解析

- 首先这个题目注意是双向释放技能，那不管怎么说，也得遍历一头一头的来杀死怪兽，那怎么杀死？要释放的技能尽可能少，只要让每个技能都发挥最大的效益就可以，既然双向释放技能，对于当前的怪兽，最好的杀死策略就是让怪兽正好位于技能释放的最左端！
- 优化点在于，我们释放技能在一个区间上减1，在区间上做操作，可以用线段树

### 代码

```java
//方法一:贪心 O(N^2)
    public static int getMinAoe1(int[] x,int[] hp,int range){
        int N = x.length;
        int res = 0;
        int rightEdge = 0,rightCenter = 0;

        for (int i = 0; i < x.length; i++) {
            if(hp[i] > 0){
                //对当前怪兽，找到最右面能打到的地方
                while (rightCenter <N && x[rightCenter] - x[i] <= range){
                    rightCenter++;
                }
                rightCenter = rightCenter-1; //右面释放AOE的位置
                while (rightEdge < N && x[rightEdge] - x[rightCenter] <= range){
                    rightEdge++;
                }
                rightEdge = rightEdge-1;
                res += hp[i];
                AoeBlood(hp,i,rightEdge,hp[i]);
            }
        }
        return res;
    }
    public static void AoeBlood(int[] hp,int left,int right,int num){
        for (int i = left; i <= right; i++) {
            if(hp[i] > 0){
                hp[i] = hp[i] - num > 0 ? hp[i] - num  : 0;
            }
        }
    }
//方法二：因为涉及到区间删除，所以可以用线段树优化成O(N*logN)
    public static class SegmentTree{
        int[] sum;
        int[] data;
        int[] lazy;

        public SegmentTree(int[] x) {
            data = Arrays.copyOfRange(x, 0, x.length);
            sum = new int[4 * x.length];
            lazy =new int[4 * x.length];
            build(1,0,x.length-1);
        }
        private void pushDown(int index,int l,int r){
            int mid = l + (r-l)/2;
            lazy[index<<1] += lazy[index];
            lazy[(index<<1)+1] += lazy[index];
            sum[index<<1] +=  (mid-l+1)*lazy[index];
            sum[(index<<1)+1] +=  (r-mid)*lazy[index];
            lazy[index] = 0;
        }
        private void build(int index,int l,int r){
            if(l == r){
                sum[index] = data[l];
                return;
            }
            int mid = l + (r-l)/2;
            build(index<<1,l,mid);
            build((index<<1)+1,mid+1,r);
            sum[index] = sum[index<<1] + sum[(index<<1)+1];
        }
        private int query(int index,int l,int r,int L,int R){
            if(l == L && r ==R){
                return sum[index];
            }
            pushDown(index,l,r);
            int mid = l + (r-l)/2;
            if(R <= mid)
                return query(index<<1,l,mid,L,R);
            else if(L >= mid+1)
                return query((index<<1)+1,mid+1,r,L,R);
            else
                return query(index<<1,l,mid,L,mid) + query((index<<1)+1,mid+1,r,mid+1,R);
        }
        private void add(int index,int l,int r,int L,int R,int elem){
            if(l == L && r == R){
                lazy[index] += elem;
                sum[index] += elem * (r-l+1);
                return;
            }
            int mid = l + (r-l)/2;
            if(R <= mid)
                add(index<<1,l,mid,L,R,elem);
            else if(L >= mid+1)
                add((index<<1)+1,mid+1,r,L,R,elem);
            else{
                add(index<<1,l,mid,L,mid,elem);
                add((index<<1)+1,mid+1,r,mid+1,R,elem);
            }
            sum[index] = sum[index<<1] + sum[(index<<1)+1];
        }
    }
    public static int getMinAoe2(int[] x,int[] hp,int range){
        int res = 0;
        final SegmentTree seg = new SegmentTree(hp);
        for (int i = 0; i < x.length; i++) {
            final int cur_hp = seg.query(1, 0, x.length - 1, i, i);
            if(cur_hp > 0){
                int center = i;
                int right = i;
                while (center < x.length && x[center]-x[i] <= range) center++;
                center--;
                while (right < x.length && x[right] - x[center] <= range) right++;
                right--;
                seg.add(1,0,x.length-1,i,right,-cur_hp);
                res+= cur_hp;
            }
        }
        return  res;
    }
```

