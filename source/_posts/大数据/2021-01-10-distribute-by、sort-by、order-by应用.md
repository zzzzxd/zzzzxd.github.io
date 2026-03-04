---
title: distribute by、sort by、order by应用
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2021-01-10 19:21:51
subtitle:
tags: hive
categories: 大数据
---

### 使用场景

#### order by：

order by关键字用于对查询结果进行全局排序。它会将所有数据收集到一个节点上进行排序，然后输出有序的结果。使用order by时，Hive会将整个数据集加载到内存中进行排序，因此适用于数据量较小的情况。然而，由于需要加载全部数据到内存，对于大规模数据集来说，可能导致内存不足的问题。
使用场景：当需要对查询结果进行全局排序，并且数据量较小的情况下，可以使用order by。

#### sort by：

sort by关键字用于对查询结果按照指定的列进行局部排序。它会将数据根据指定的列进行排序，但不保证全局有序。Hive会将数据按照指定的列进行划分和排序，每个划分内的数据是有序的，但不同划分之间的顺序是未定义的。sort by可以配合使用分区（partition）来实现更细粒度的排序。
使用场景：当需要按照指定的列对查询结果进行局部排序，而不要求全局有序时，可以使用sort by。

#### distribute by：

distribute by关键字用于指定数据的分发方式。它用于控制将数据发送到不同的reducer节点上。distribute by通常与sort by或cluster by一起使用，以控制数据的分区和排序。
使用场景：当需要按照指定的列对数据进行分发，并且可能需要后续的排序操作时，可以使用distribute by。

#### cluster by：

cluster by关键字用于对数据进行分区和排序。它类似于distribute by，但它会尝试将数据按照指定的列进行排序，并将相邻的值放置在相同的分区中。cluster by会自动执行分区和排序的操作。
使用场景：当需要将数据按照指定的列进行分区和排序，并且希望相邻值在同一分区中时，可以使用cluster by。

### 案例解释

#### sort by + distribute by

```sql
需求：获取每个商品类别和国家下的商品个数，并按照商品类别和国家下的商品销售取前三名。

数据：

类别 国家 商品名称 销售数量
movies us movies_us_1 100
movies us movies_us_2 150
movies us movies_us_3 200
movies us movies_us_4 300
movies gb movies_gb_1 100
movies gb movies_gb_2 150
movies gb movies_gb_3 200
movies gb movies_gb_4 300
office gb office_gb_1 30
office gb office_gb_2 40
office gb office_gb_3 50
office gb office_gb_4 60
office us office_us_1 30
office us office_us_2 50
office us office_us_3 60
office us office_us_4 70
按照商品类别和国家下的商品销售排名(按照category,country分组，并通过sort by每个分组的结果排序)

select
  category,
  country,
  product, 
sales,
rank() over(distribute by category,country sort by category,country, sales desc)  as rk 
from p_rank_demo;

或者通过
select  
	category,
	country,
	product, 
	sales,
	rank() over(partition by category,country order by sales desc)  as rk 
from p_rank_demo;

输出通过的结果（两者的区别： distribute by 具有相同的类别和国家的记录都发送到同一个reducer上，这样就可以统计
出每个类别和国家的销售商品的排名了。若业务是全局排序，可以通过partition by order by 获取排名。）

category country product       sales   rk
movies  gb      movies_gb_4     300     1
movies  gb      movies_gb_3     200     2
movies  gb      movies_gb_2     150     3
movies  gb      movies_gb_1     100     4
movies  us      movies_us_4     300     1
movies  us      movies_us_3     200     2
movies  us      movies_us_1     100     3
movies  us      movies_us_2     100     3
office  gb      office_gb_4     60      1
office  gb      office_gb_3     50      2
office  gb      office_gb_2     40      3
office  gb      office_gb_1     30      4
office  us      office_us_4     70      1
office  us      office_us_3     60      2
office  us      office_us_2     50      3
office  us      office_us_1     30      4

按照商品类别和国家下的商品销售排名前三输出
select 
* 
from (
  select  
  	category,
  	country,
  	product,
  	sales,
  	rank() over(distribute by category,country sort by category,country, sales desc)  as rk 
  from p_rank_demo
) t 
where t.rk <= 3;
```



### 使用小结

