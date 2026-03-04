---
title: CI、CD系列三之全流程部署开源项目zrlog
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-12-20 20:12:52
subtitle:
tags:
  -	LINUX
categories:
  -	LINUX运维
---

## 概述

​	我们以java开源项目zrlog为例，进行一次完整的部署，实现一套完整的CICD流程。

​	整个过程如下：

1. 开发提交代码至gitlab

2. jenkins拉取获取代码

3. 代码的质量检测;

4. 代码的编译;

5. 发布。

## 环境准备

### 源码准备 

```shell
[root@localhost code]# git clone https://gitee.com/94fzb/zrlog.git
[root@localhost zrlog]# yum install -y nodejs npm 
[root@localhost zrlog]# npm install -g yarn
```

### JDK 准备

```shell
[root@localhost bin]# java -version
java version "1.8.0_351"
Java(TM) SE Runtime Environment (build 1.8.0_351-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.351-b10, mixed mode)
```

### maven准备

```shell
[root@localhost opt]# wget -P /apps/tools/ https://archive.apache.org/dist/maven/maven3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz 
#配置 maven源
<mirror> 
			<id>aliyunmaven</id> 
			<mirrorOf>*</mirrorOf> 
			<name>阿里云公共仓库</name>
			<url>https://maven.aliyun.com/repository/public</url> 
</mirror>
```

#### 编译源码

![image-20221221112148589](image-20221221112148589.png)

### 数据库准备 

```mysql

mysql> create database zrlog ;
Query OK, 1 row affected (0.02 sec)

mysql> grant all on zrlog.* to zrlog@'%' identified by '123456';
Query OK, 0 rows affected, 1 warning (0.03 sec)
```

### 推送Gitlab

![image-20221221103226086](image-20221221103226086.png)

如图，我们给源码打一个tag 部署时候，就按照标签进行构建。

## Jenkins构建

### 整合gitlab

![image-20221221103901057](image-20221221103901057.png)

![image-20221221104109071](image-20221221104109071.png)

  这个地方,我们调整参数化构建，拉取指定的tag版本。

![image-20221221110504480](image-20221221110504480.png)

 接下来，就可以进行构建了。

![image-20221221110617350](image-20221221110617350.png)

查看日志

![image-20221221111051117](image-20221221111051117.png)

现在 构建过程主要是，拉取git代码，进行maven编译
