---
title: Problem 11 设计setAll
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2020-07-28 14:42:43
subtitle:
tags:
  -	algorithm
categories:
  -	算法
    - 企业面试题
---

### 题目

​	设计有setAll功能的哈希表

​	put、get、setAll方法，时间复杂度O(1)

### 题目解析

- 考察思想 “版本”，利用时间戳做版本的思想。

### 代码

```java
public static class MyValue<V> {
		public V value;
		public long time;

		public MyValue(V v, long t) {
			value = v;
			time = t;
		}
	}

	public static class MyHashMap<K, V> {
		private HashMap<K, MyValue<V>> map;
		private long time;
		private MyValue<V> setAll;

		public MyHashMap() {
			map = new HashMap<>();
			time = 0;
			setAll = new MyValue<V>(null, -1);
		}

		public void put(K key, V value) {
			map.put(key, new MyValue<V>(value, time++));
		}

		public void setAll(V value) {
			setAll = new MyValue<V>(value, time++);
		}

		public V get(K key) {
			if (!map.containsKey(key)) {
				return null;
			}
			if (map.get(key).time > setAll.time) {
				return map.get(key).value;
			} else {
				return setAll.value;
			}
		}
	}
```

