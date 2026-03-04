---
title: (四)HBASE SHELL
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2023-02-16 23:13:24
subtitle:
tags: Hbase
categories: HBase
---

提示：可以查看命令组，或者是命令的帮助信息

```shell
hbase(main):000:0> help
hbase(main):000:0> help 'command group'
hbase(main):000:0> help 'command'
```

## Namespace操作

**1）** list_namespace:查询所有命名空间

```shell
hbase(main):020:0> list_namespace
```

**2）** list_namespace_tables: 查询指定命名空间的表

```shell
hbase(main):022:0> list_namespace_tables 'myns'  
```

**3）** create_namespace: 创建指定的命名空间

```shell
hbase(main):023:0> create_namespace 'myns'
hbase(main):024:0> list_namespace
```

**4）**describe_namespace: 查询指定命名空间的描述信息(也叫结构信息，属性信息)

```shell
hbase(main):025:0> describe_namespace 'ns4'
```

**5）** alter_namespace ：修改命名空间的描述信息

```shell
添加描述信息
hbase(main):026:0> alter_namespace 'ns4', {METHOD => 'set', 'name' => 'michael'}
hbase(main):027:0> describe_namespace 'ns4'

删除描述信息
hbase(main):028:0> alter_namespace 'ns4', {METHOD => 'unset', NAME => 'name'}
hbase(main):029:0> describe_namespace 'ns4'
```

**6）** drop_namespace:删除指定命名空间

```shell
hbase(main):030:0> drop_namespace 'ns4'
hbase(main):031:0> list_namespace
```

## Table的DDL操作

**1）** 建表：必须至少指定一个列族名

```sh
- 直接创建
  hbase(main):034:0> create 'ns3:emp','base_info'

- 使用变量
  hbase(main):036:0> t = create 'ns3:employee','base_info'

- 创建多个列族名
  hbase(main):040:0> create 'ns3:emp1','f1','f2','f3'

- 创建表时同时指定列族属性
  hbase(main):043:0> create 'ns3:emp2',
  {NAME=>'f1',VERSIONS=>3,TTL=>2592000,BLOCKCACHE=>TRUE}

- 创建表同时指定分片
  hbase(main):050:0> create 'ns3:emp3', 'f1', SPLITS => ['10', '20', '30', '40']
```

**2）**查看表属性信息

```shell
hbase(main):051:0> describe 'ns3:emp1'
```

**3）** 列出所有的表

```shell
hbase(main):052:0> list
```

**4）** 修改表属性

```shell
hbase(main):062:0> alter 'ns3:emp3',{NAME=>'f1',BLOOMFILTER=>'rowcol',VERSIONS=>5,TTL=>2592000}

hbase(main):063:0> describe 'ns3:emp3'
```

**5）** 追加列族：

```shell
- 方式1
  hbase(main):064:0> alter 'ns3:emp3',{NAME=>'f2'}

- 方式2
  hbase(main):066:0> alter 'ns3:emp3','f3'

- 方式3：
  hbase(main):070:0> alter 'ns3:emp3','f5','f6'
```

**6）** 删除列族

```shell
hbase(main):075:0> alter 'ns3:emp3','delete'=>'f6'
```

**7）** 删除表

```shell
先禁用，再删除
hbase(main):076:0> disable 'ns3:emp3'
hbase(main):078:0> drop 'ns3:emp3'
```

## Table的CRUD操作

**1）** 插入数据：put只能插入一个单元格

```shell
指定 表名、行键、列名、列值、[时间戳]

hbase(main):081:0> put 'ns3:emp','rk0001','base_info:name','smith'
hbase(main):084:0> put 'ns3:emp','rk0001','base_info:empno','7369',1990000000
```

**2）** 使用scan查询数据

```shell
1. 浏览表所有数据：已经排过序，先按rowkey,再按列族名、然后按列名

   hbase(main):085:0> scan 'ns3:emp' 

2. 指定版本数量进行浏览

   hbase(main):110:0> scan 'ns3:emp',{VERSIONS=>3}
   hbase(main):110:0> scan 'ns3:emp',VERSIONS=>3

3. 指定列进行浏览

   hbase(main):122:0> scan 'ns3:emp',{COLUMNS=>'base_info:empno'}
   scan 'ns3:emp',{COLUMNS=>['base_info:empno','extra_info:province']} 

4. 限定行范围进行浏览(分页查询)

   hbase(main):137:0> scan 'ns3:emp',
   {COLUMNS=>'base_info:empno',LIMIT=>5,STARTROW=>'rk0001'}
   
   hbase(main):136:0> scan 'ns3:emp',{LIMIT=>5,STARTROW=>'rk0001'}
```

**3）** 使用get进行指定行浏览该行内的数据

```shell
1. 查询指定行 hbase(main):140:0> get 'ns3:emp','rk0001'

2. 查询指定行中的列

   hbase(main):155:0> get 'ns3:emp','rk0001','base_info:empno'
   hbase(main):155:0> get 'ns3:emp','rk0001',{COLUMN=>'base_info:empno'}
   hbase(main):157:0> get 'ns3:emp','rk0001','base_info:name','extra_info:province'    
   hbase(main):145:0> get 'ns3:emp','rk0001',{COLUMN=>
   ['base_info:empno','extra_info:province']}

3. 指定版本号

   hbase(main):146:0> get 'ns3:emp','rk0001',{COLUMN=>['base_info:empno','extra_info:province'],VERSIONS=>3}

4. 指定时间戳的范围

   hbase(main):152:0> get 'ns3:emp','rk0001',{TIMERANGE=>[1568619337238,1568619348753]}
```

**4）** 更新数据

```shell
1. 使用put

   hbase(main):159:0> put 'ns3:emp','rk0001','base_info:name','smith'

2. 使用incr，进行自动赋值  

   incr 'ns3:emp','rk0001','base_info:age'
```

**5）** 删除数据：

```shell
1. 删除某行内的指定key-value对

   hbase(main):001:0> delete 'ns3:emp','rk0001','base_info:name'

2. 删除指定版本号的单元格

   hbase(main):008:0> delete 'ns3:emp','rk0001','base_info:empno',1568619337238

3. 删除指定行

   hbase(main):011:0> deleteall 'ns3:emp','rk0008'
```

**6）** 判断表是否存在

```shell
hbase(main):013:0> exists 'ns3:emp'
```

**7）** 禁用表和启用表

```shell
hbase(main):014:0> disable 'ns3:emp'
hbase(main):016:0> enable 'ns3:emp'
```

**8）** 统计表行数

```shell
hbase(main):018:0> count 'ns3:emp'
```

**9）** 清空表数据

```shell
hbase(main):019:0> truncate 'ns3:emp'
```

