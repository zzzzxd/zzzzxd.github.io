---
title: linux 停止启动mysql常用命令
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-03-06 19:56:29
subtitle:
tags:
  - mysql
categories:
  -	Mysql
---

### 查看mysql版本

方法一：status;

方法二：select version();

### Mysql启动、停止、重启常用命令

```mysql
a、启动方式
1、使用 service 启动：
    [root@localhost /]# service mysqld start (5.0版本是mysqld)
    [root@szxdb etc]# service mysql start (5.5.7版本是mysql)
2、使用 mysqld 脚本启动：
    /etc/inint.d/mysqld start
3、使用 safe_mysqld 启动：
    safe_mysqld&
b、停止
    1、使用 service 启动：
    	service mysqld stop
    2、使用 mysqld 脚本启动：
    	/etc/inint.d/mysqld stop
    3、mysqladmin shutdown
c、重启
    1、使用 service 启动：
    	service mysqld restart
    	service mysql restart (5.5.7版本命令)
    2、使用 mysqld 脚本启动：
    	/etc/init.d/mysqld restart
```

