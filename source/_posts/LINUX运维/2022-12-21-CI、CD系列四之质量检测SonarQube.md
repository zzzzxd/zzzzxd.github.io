---
title: CI、CD系列四之质量检测SonarQube
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-12-21 21:15:04
subtitle:
tags:
  -	LINUX
categories:
  -	LINUX运维
---

## 前言

SonarQube是代码质量检测的利器，可以说自动化运维工具必备，下面我们就使用它对开源项目进行检测，观察一下。

## 环境准备 



```shell
[root@linx01 sonarqube-7.7]# pwd
/opt/apps/sonarqube-7.7
[root@linx01 conf]# vim sonar.properties 
```

![image-20221221212350654](image-20221221212350654.png)

```shell
[sonar@localhost linux-x86-64]$ ./sonar.sh start
Starting SonarQube...
Started SonarQube.
```

![image-20221221215007422](image-20221221215007422.png)

## 分析java项目

首先创建项目，用来绑定要检查的项目，查看结果的

![image-20221221215335236](image-20221221215335236.png)

![image-20221221215432390](image-20221221215432390.png)

提示使用这串命令来编译项目

```shell
mvn sonar:sonar \
  -Dsonar.projectKey=java-simple \
  -Dsonar.host.url=http://192.168.1.7:29000 \
  -Dsonar.login=a74d6b221e2154c48632e1b85f5940beabe91205
```

我们在gitlab拉取下来的项目，进行分析

![image-20221221222744652](image-20221221222744652.png)

在sonar的页面山也显示了扫描的结果

![image-20221221222854103](image-20221221222854103.png)

 可以看到具体的bug

![image-20221221222911136](image-20221221222911136.png)
