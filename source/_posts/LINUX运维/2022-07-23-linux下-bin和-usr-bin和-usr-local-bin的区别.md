---
title: ' linux下 bin和 usr bin和 usr local bin的区别'
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-23 21:43:35
subtitle:
tags:
  -	LINUX
categories:
  -	LINUX运维
---

```shell
/bin 是所有用户都可以访问并执行的可执行程序。包括超级用户及一般用户。
/usr/bin 是系统安装时自带的一些可执行程序。即系统程序，轻易不要去动里面的东西，容易入坑。
/usr/local/bin 是用户自行编译安装时默认的可执行程序的安装位置，这个不小心误删点什么，不会影响大局。
```

