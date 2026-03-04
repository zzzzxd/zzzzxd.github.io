---
title: watermark的传递
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2024-01-06 19:38:18
subtitle:
tags: 
  - Flink
  - Flink sql
categories: Flink
---

## 水位线的传递

![image-20240106194023686](1.png)

​		在流处理中，上游任务处理完⽔位线、时钟改变之后，要把当前的⽔位线再次发出，⼴ 播给所有的下游⼦任务。⽽当⼀个任务接收到多个上游并⾏任务传递来的⽔位线时，应该以 最⼩的那个作为当前任务的事件时钟。

​		⽔位线在上下游任务之间的传递， ⾮常巧妙地避免了分布式系统中没有统⼀时钟的问 题，每个任务都以“处理完之前所有数据”为标准来确定⾃⼰的时钟。

## 水位线未推进问题

​	生产中，当消费kafka 多分区时，如果某些分区因为倾斜没有数据，而水位线又取的是多个分区的最小值，就会导致水位线不会被触发。具体可参考官网：https://nightlies.apache.org/flink/flink-docs-master/zh/docs/dev/datastream/event-time/generating_watermarks/

#### 解决方案

通过在flink内部添加参数，设定多长时间没有消息时就舍弃此分区，可以参考文章:https://mp.weixin.qq.com/s/108o9iwEZaHyMoRBGUKX8g。 实际上Flink官方也提供了相关入口

```java
WatermarkStrategy
        .<Tuple2<Long, String>>forBoundedOutOfOrderness(Duration.ofSeconds(20))
        .withIdleness(Duration.ofMinutes(1));
```

Flink sql 也有相关参数

```sql
table.exec.source.idle-timeout: '1000' #单位毫秒
```

