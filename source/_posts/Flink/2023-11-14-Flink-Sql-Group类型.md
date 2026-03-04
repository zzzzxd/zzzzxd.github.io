---
title: Flink Sql Group类型
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2023-11-14 19:23:13
subtitle:
tags: 
  - Flink
  - Flink sql
categories: Flink
---

## Group Aggregation

### 注意事项

- 对于流式查询，计算查询结果所需的状态可能会无限增长。状态大小取决于组的数量以及聚合函数的数量和类型。例如，MIN/MAX在状态大小上很重，而COUNT则很便宜。您可以为查询配置提供适当的生存时间（TTL），以防止状态大小过大。请注意，这可能会影响查询结果的正确性

### 语法

​	同mysql 语法

### 用法举例

```java
SELECT COUNT(*)
FROM Orders
GROUP BY order_id
```

## Window Aggregation

### Window TVF Aggregation

#### TUMBLE TVF

##### 语法

` TUMBLE(TABLE data, DESCRIPTOR(timecol), size [, offset ])`

##### 用法举例

``` sql
SELECT window_start, window_end, SUM(price)
  FROM TABLE(
    TUMBLE(TABLE Bid, DESCRIPTOR(bidtime), INTERVAL '10' MINUTES))
  GROUP BY window_start, window_end;
```

#### HOP TVF

##### 语法

`HOP(TABLE data, DESCRIPTOR(timecol), slide, size [, offset ])`

##### 用法举例

 ```sql
 SELECT window_start, window_end, SUM(price)
   FROM TABLE(
     HOP(TABLE Bid, DESCRIPTOR(bidtime), INTERVAL '5' MINUTES, INTERVAL '10' MINUTES))
   GROUP BY window_start, window_end;
 ```

#### CUMULATE TVF

##### 语法

`CUMULATE(TABLE data, DESCRIPTOR(timecol), step, size)`

##### 用法举例

```sql
SELECT window_start, window_end, SUM(price)
  FROM TABLE(
    CUMULATE(TABLE Bid, DESCRIPTOR(bidtime), INTERVAL '2' MINUTES, INTERVAL '10' MINUTES))
  GROUP BY window_start, window_end;
```

### Group Window Aggregation

#### TUMBLE

##### 语法

`TUMBLE(time_attr, interval)`

##### 用法举例

#### TUMBLE

##### 语法

`HOP(time_attr, interval, interval)`

##### 用法举例

```sql
SELECT
  user,
  TUMBLE_START(order_time, INTERVAL '1' DAY) AS wStart,
  SUM(amount) FROM Orders
GROUP BY
  TUMBLE(order_time, INTERVAL '1' DAY),
  user
```

#### TUMBLE

##### 语法

`HOP(time_attr, interval, interval)`

##### 用法举例

​	同Hop

#### TUMBLE

##### 语法

`SESSION(time_attr, interval)`

##### 用法举例

​	同Hop

## 小结

- 普通的group 基于状态的，会一直持续保留group的结果，可以结合状态TTL，但是如果状态过期值会消失，会直接影响到聚合值的正确性，所以推荐window的聚合，虽然窗口会关闭 ，可以通过watermark会降低误差，及时因为延迟有未处理的数据，也只是误差的问题 不会导致结果错误。

- table value function （tvl） 和 group window  fucntion 都是做聚合group 可以选择的语法，前者是后者的封装，后者逐渐废弃⚠️,前者功能很强

   >Warning: Group Window Aggregation is deprecated. It’s encouraged to use Window TVF Aggregation which is more powerful and effective.
   >
   >Compared to Group Window Aggregation, Window TVF Aggregation have many advantages, including:
   >
   >- Have all performance optimizations mentioned in [Performance Tuning](https://nightlies.apache.org/flink/flink-docs-release-1.18/docs/dev/table/tuning/).
   >- Support standard `GROUPING SETS` syntax.
   >- Can apply [Window TopN](https://nightlies.apache.org/flink/flink-docs-release-1.18/docs/dev/table/sql/queries/window-topn/) after window aggregation result.
   >- and so on.

- tvl 还不支持session 窗口的语法，如果有session 窗口这种场景还得用老语法。
- tvl 增加了 cumulate 窗口，可以实现窗口连续输出消息～
