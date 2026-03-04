---
title: Mysql ONLY_FULL_BY_GROUP_MODE模式
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-14 21:57:45
subtitle:
tags:
  -	mysql
categories:
  -	Mysql
---

**ONLY_FULL_BY_GROUP_MODE**

​	如果此模式不开启，则对mysql的扩展生效 此时与标准sql 有两个明显的区别：

​	**允许在select list，having condition 和 order by 中使用没有出现在group by 中的字段，此时mysql会随机选择没有出现在group by字段中的值，其效果和使用ANY—VALUE效果是一样的**

**允许在having condition 中使用select list 中的alias**

