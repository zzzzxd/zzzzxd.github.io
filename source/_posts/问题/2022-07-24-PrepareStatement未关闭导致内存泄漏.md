---
title: PrepareStatement未关闭导致内存泄漏
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-24 00:08:58
subtitle:
tags:
  - Trouble Shooting
categories:
  - 常见问题
---

### 现象

堆内存飙升，GC次数明显提升

### 原因:

第三方的数据库连接池，使用的时候，获取到Connection之后，使用完成，调用的关闭方法（close()） ，并没有将Connection关闭，只是放回到连接池中，如果调用的这个方法，而没有手动关闭PreparedStatement等，则这个PreparedStatement并没有关闭，这样会使得开发的程序内存急速增长，java的内存回收机制可能跟不上速度，最终造成Out of memory Error

今天，就犯了一个很离谱的错误，ps没关，不断new新的ps，导致堆内存不断飙升，GC就是回收不了，结果OOM～

GC不掉的原因

引用百度问答的一段话：

1.你创建了100个PS的实例，但是你只关闭了最后一个。1楼说的GC会回收另外99个，没错，但GC能自动回收的只是JAVA创建的资源。preparedstatement为jdbc得api，他还会创建跟具体数据库有关的其他资源。close方法就是用来施放那些资源的。如果你没有每个都close，会导致内存泄漏。

2.不可以。------------补充：当然有非java创建的资源 jdbc可以说是一个和数据库通信的驱动。你建立jdbc的对象，调用它的方法。jdbc会去调用你使用的数据库的api，实现对数据库的操作。那些数据库的api会创建很多资源。java的gc（垃圾回收器）只会释放掉那些java对象所占的内存。无法释放数据库api创建的资源。只有通过jdbc相关的close方法才能释放。你只要记住，所有有close方法的jdbc对象在使用完成后都需要手动释放。另外，就是内存泄漏，相应的资源在内存中没有被引用，而又没有被操作系统回收。对，就是这个意思，除了JDBC还有很多根操作系统api有关的java api都有close方法，他们都是用来释放相应的非java资源的。

引用几篇文章，了解一下这个ps和现场排查的过程

[JDBC：深入理解PreparedStatement和Statement](https://www.jianshu.com/p/50a33f2046dd)

[**故障分析报告 - Java应用PS未关闭导致的OOM错误**](http://www.jiaqili.me/post/oom-profiling-report-20181121/)
