---
title: Flink Sql Join类型
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2023-10-11 10:09:53
subtitle:
tags: 
  - Flink
  - Flink sql
categories: Flink
---

### Regular Joins(普通join)

#### 注意事项

- 最普通的join 两条流任何一条流更新都会触发计算,为了支持结果的更新，下游目的表需要定义主键。
- 运行时需要保留左表和右表的状态，且随着时间的推移，状态会无限增长，最终可能导致作业 OOM 崩溃或异常缓慢。因此我们强烈建议用户在 Flink 参数中设置 table.exec.state.ttl 选项，它可以指定 JOIN 状态的保留时间（定义键的状态在多长时间内没被更新过就会被删除），以便 Flink 及时清理过期的状态，默认值是0 ms，即保存所有状态。比如设置为1天，SET 'table.exec.state.ttl' = '86400 s';

#### 语法

​	和普通的数据库join一样，支持INNER、LEFT、RIGHT 等多种 JOIN 类型，但是目前仅支持等值条件的连接（on x=y)

#### 使用示例

   ```java
   SELECT *
   FROM Orders
   INNER JOIN Product
   ON Orders.product_id = Product.id
   ```

### Interval Joins(区间join)

#### 注意事项

- 需要至少一个等值关联条件 和 时间限制条件，时间限制条件可以是等值、条件比较、或者Between，如下ltime 左表时间属性 rtime 右表时间属性
  - ltime = rtime
  - ltime >= rtime AND ltime < rtime + INTERVAL '10' MINUTE
  - ltime BETWEEN rtime - INTERVAL '10' SECOND AND rtime + INTERVAL '5' SECOND
- 该类型join只支持Append 数据流，不支持含 Retract等 的动态表。由于给定了关联的区间，因此只需要保留很少的状态，内存压力较小。但是缺点是如果关联的数据晚到或者早到，导致落不到 JOIN 区间内，就可能导致结果不准确。此外，只有当区间过了以后，JOIN 结果才会输出，因此会有一定的延迟存在。

#### 语法

	1. 两个表中指定时间属性
	1. Join条件中 需要包含至少一个等值关联条件  外加一个时间区间的条件

#### 使用示例

```java
SELECT *
FROM Orders o, Shipments s
WHERE o.id = s.order_id
AND o.order_time BETWEEN s.ship_time - INTERVAL '4' HOUR AND s.ship_time
```

### Temporal Joins（时态表join）

#### Event Time Temporal Join

#### Processing Time Temporal Join

### Lookup Join（查找join)

#### 注意事项

- Lookup Join常用于流与维表之间的join，维表来源于外部系统，比如mysql、redis等。而维表的作用就是对流式数据进行数据补全

- 的，这要在性能和正确性之间做权衡。所以Lookup Join也提供了缓存的机制，使用的是guava cache实现的。默认缓存机制是禁用的。

  - lookup.cache.max-rows 和 lookup.cache.ttl。两个都配置了才能启用缓存。

    lookup.cache.max-rows：指定缓存的最大行数。如果超过该值，则最老的行记录将会过期，会被新的记录替换掉。

    lookup.cache.ttl：指定缓存中每行记录的最大存活时间。如果某行记录超过该时间，则该行记录将会过期。

    lookup.cache.caching-missing-key：是否缓存空查询结果的key，默认值是true，即缓存空查询结果的key（即key值没查到结果）

    lookup.max-retries：查询数据库的最大重试次数

#### 语法

​		Lookup Join要求流表具有处理时间属性（必须是processing time，当前不支持event time，也就是说这种方法不支持根据数据流的事件时间去查维度表里的对应时刻的数据），而维表可以直接使用连接器connector支持。Lookup Join使用Processing Time Temporal Join 的语法，且必须要有等值连接条件。

​	关键写法：JOIN table2 FOR SYSTEM_TIME AS OF table1.proctime

#### 使用示例

```java
CREATE TABLE orders (
    order_id    STRING,
    price       DECIMAL(32,2),
    customer_id    STRING,
    order_time  TIMESTAMP(3),
    proc_time as PROCTIME()
) with (
  'connector' = 'kafka',
  'topic' = 'mdxxx',
  'properties.bootstrap.servers' = 'xxx-003:9092',
  'properties.group.id' = 'xxx',
  'scan.startup.mode' = 'latest-offset',
  'format' = 'json',
  'json.fail-on-missing-field' = 'false',
  'json.ignore-parse-errors' = 'true'
);
CREATE TABLE customers (
  id INT,
  name STRING,
  country STRING,
  zip STRING
) with (
  'connector' = 'jdbc',
  'url' = 'jdbc:mysql://mysqlhost:3306/customerdb',
  'table-name' = 'da.customers',
  'username' = 'xxx',
  'password' = 'xxx',
  'lookup.cache.max-rows' = '100000',
  'lookup.cache.ttl' = '86400000'
);
SELECT o.order_id, o.price, c.country, c.zip
FROM orders AS o
  JOIN customers FOR SYSTEM_TIME AS OF o.proc_time AS c
    ON o.customer_id = c.id;
```

### Table Function Join（表函数join）

#### 注意事项

​	将表与表函数的结果join起来。左边(外部)表的每一行都与表函数的相应调用所产生的所有行join。用户定义表函数必须在使用前注册

#### 语法

​	`JOIN LATERAL TABLE(table_func(filed))`

#### 示例

```java
SELECT order_id, res
FROM Orders,
LATERAL TABLE(table_func(order_id)) t(res)
SELECT order_id, res
FROM Orders
LEFT OUTER JOIN LATERAL TABLE(table_func(order_id)) t(res)
ON TRUE
```

### 总结

