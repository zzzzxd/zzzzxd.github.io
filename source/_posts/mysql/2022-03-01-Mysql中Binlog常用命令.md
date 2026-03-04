---
title: Mysql中Binlog常用命令
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-03-01 19:50:00
subtitle:
tags:
  - mysql
categories:
  -	Mysql
---

### Binlog使用场景

- 主从数据库复制： Mysql Replication在Master端开启Binlog，Master把日志传递给Slavers，以达到	Master-slavers主从数据同步。
- 数据恢复： 使用mysqlbinlog工具来恢复数据

### Binlog常用命令

 ```mysql
 # 查看是否启用Binlog日志
 show variables like 'log_bin';
 
 # 查看详细的日志配置信息
 show global variables like '%log%';
 
 # Mysql数据存储目录
 show variables like '%dir%';
 
 # 查看binlog数据目录
 show global variables like '%log_bin%';
 
 ##以下命令需要super权限
 # 查看当前服务器binlog文件及大小
 show binary logs;
 
 # 查看 binlog 内容
 show binlog events;
 
 # 查看具体一个binlog文件的内容 （in 后面为binlog的文件名）
 show binlog events in 'master.000003';
 
 # 设置binlog文件保存事件，过期删除，单位天
 set global expire_log_days=3; 
 
 # 删除当前的binlog文件
 reset master; 
 
 # 删除slave的中继日志
 reset slave;
 
 # 删除指定日期前的日志索引中binlog日志文件
 purge master logs before '2019-03-12 21:54:00';
 
 # 删除指定日志文件
 purge master logs to 'master.000001';
 
 ```

