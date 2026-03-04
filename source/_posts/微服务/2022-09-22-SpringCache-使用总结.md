---
title: SpringCache 使用总结
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-09-22 19:46:15
subtitle:
tags:
  - 缓存
  - Spring Cache
categories:
  -	微服务
---

## Spring Cache

### 简介

- Spring 从 3.1 开始定义了 org.springframework.cache.Cache 和 org.springframework.cache.CacheManager 接口来统一不同的缓存技术； 并支持使用 JCache（JSR-107）注解简化我们开发；

- Cache 接口为缓存的组件规范定义，包含缓存的各种操作集合； Cache 接 口 下 Spring 提 供 了 各 种 xxxCache 的 实 现 ； 如 RedisCache ， EhCacheCache , ConcurrentMapCache 等；
- 每次调用需要缓存功能的方法时，Spring 会检查检查指定参数的指定的目标方法是否已 经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法并缓 存结果后返回给用户。下次调用直接从缓存中获取。
- 使用 Spring 缓存抽象时我们需要关注以下两点； 
  - 确定方法需要被缓存以及他们的缓存策略 
  - 从缓存中读取之前缓存存储的数据

### 基础概念

![image-20220923101141154](1.jpg)

### 注解

![image-20220923101355445](2.jpg)

### 表达式语法

![WeChatd3233d7e110a63f5fa8dfb06822727cd](4.jpg)

### 缓存穿透问题解决

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

允许 null 值缓存
