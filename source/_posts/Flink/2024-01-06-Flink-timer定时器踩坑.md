---
title: Flink timer定时器踩坑
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2024-01-06 23:10:45
subtitle:
tags: 
  - Flink
categories: Flink
---

## 问题

公司同事在开发时，发现任务跑一段时间后 定时器会延迟执行，查看日志如下:



<img src="1.png" alt="2031704554198_.pic" style="zoom:80%;" />

延迟了一个小时,基本上高峰就会出现。

查看火焰图

![image-20240106231800487](3.png)

​	火焰图中明显看到定时器的处理方法占用大量cpu时间，所以定时器这块多少是有点问题的，但问题到底出在哪，只能进一步结合代码来看。

<img src="4.png" alt="image-20240106232240344" style="zoom:80%;" />

可以看到代码中是每条数据都会注册定时器，也就是说 同时来100条数据，就会同时注册100个定时器，并且注册的定时器时间都一样，都会在同一时间调起，这样看来 主要原因就是定时器太多了，导致执行的慢，那这又是为什么，定时器多了 flink就解决不了吗？纵使加了一倍的资源也没有好转，而且从整体的cpu指标上都很正常，任务也没有背压。

![image-20240106232629314](5.png)

![image-20240106232644089](8.png)

<img src="6.png" alt="image-20240106232736753" style="zoom:80%;" />

算子报红，是因为包含处理逻辑 要繁忙一点，并没出现背压的情况，数据处理也没有延迟。下一步只能从定时器这块代码入手了

阅读相关源码发现 InternalTimerService 执行的注册操作，最终还是由ScheduledTaskExecutor执行，并且，线程池的核心线程只有一个线程，难怪执行比较慢了。

InternalTimerServiceImpl.java

![image-20240318184441095](9.png)

processingTimeService 为 SystemProcessingTimeService

![image-20240318184602962](10.png)

timerService 就是jdk的ScheduledTaskExecutor 如图:

![image-20240318184655824](11.png)



如此一来就清晰了，因为在OnTimer()方法中调用了外部接口，单接口调用是平均5ms，也就是意味着一个TM每秒最多处理200个定时任务，而线上相关的触达数据每秒最高125左右，会注册执行完后继续注册每次注册推迟30min会持续24h,也就是极端情况下，最高需要每秒处理7000的定时任务。。。这样一来肯定会延迟执行了。

## 解决方案 

​	问题分析清楚，相对就好解决了。如果还采用定时器的方案，就只能优化定时任务的消耗时间，如果单定时任务耗费在0.1ms，那就有1万的并发处理能力也就还ok，再者尽可能咱批，减少定时任务的数量
