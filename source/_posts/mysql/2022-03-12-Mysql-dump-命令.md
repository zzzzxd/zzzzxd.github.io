---
title: Mysql dump 命令
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-03-12 20:07:23
subtitle:
tags:
  - mysql
categories:
  -	Mysql

---

### 命令及解释

```mysql
mysqldump -ubackup -p123456 -h 192.168.1.2 backup_test > D:\bak\bakcup.sql 
	在这段命令中：mysqldump 是myqldump的程序名了；
  -u 是数据库的用户名，后面紧跟着用户名backup；
  -p 是数据库的密码，后面同样紧跟着密码，注意是-p和密码之间不能有空格；
  -h 是数据库的地址，如果没有此项表明是备份本地的数据库；
	(对我们用的阿里云，就是rds暴露在外的公网地址  xxxx.mysql.rds.aliyuncs.com)
	backup_test 要备份的数据库名称；
	表明该数据库备份到后面的文件，紧跟当然是备份文件的地址了，注意要用绝对文件路径，文件的后缀也可以用.txt。
	一旦执行该命令后，mysqldump立即执行备份操作。远程备份数据库要似乎网络的情况，一般远程备份需要一定的时间的。
```

### 小结

实际mysql dump记住两条命令就够了

```mysql
导入：
mysql -u root -p database < data.sql
导出：
（整个数据库）
mysqldump -u 账户 -p密码 数据库名字 > data.sql
（单个表）
mysqldump  -u 账户 -p密码 数据库名字 表名字 > data.sql
```



>补充：
>
>导出一个表，只有表结构
>
>mysqldump -u用户名 -p 密码 -d数据库名 表名> 导出的文件名
>
>C:\Users\jack> mysqldump -uroot -pmysql -d sva_rec date_rec_drv> e:\date_rec_drv.sql
>
>导出一个数据库结构
>
>C:\Users\jack> mysqldump -uroot -pmysql -d sva_rec > e:\sva_rec.sql
