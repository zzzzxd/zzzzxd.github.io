---
title: docker tag命令解释
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-22 19:13:19
subtitle:
tags:
  -	DOCKER
categories:
  - docker
---

###  命令

```docker
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

### 示例演示

1. 比如我现在有一个 centos 镜像：

```docker
[root@localhost ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE 
centos              latest              1e1148e4cc2c        2 weeks ago         202MB
```

2. 我对 centos 进行开发，开发了第一个版本，我就可以对这个版本打标签，打完标签后会生成新的镜像：

```docker
[root@localhost ~]$ docker tag centos centos:v1
[root@localhost ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE 
centos              latest              1e1148e4cc2c        2 weeks ago         202MB
centos              v1                  1e1148e4cc2c        2 weeks ago         202MB
```

3. 我继续对 centos 进行开发，开发了第二个版本，继续打标签：

```docker
[root@localhost ~]$ docker tag centos centos:v2
[root@localhost ~]$ docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              1e1148e4cc2c        2 weeks ago         202MB
centos              v1                  1e1148e4cc2c        2 weeks ago         202MB
centos              v2                  1e1148e4cc2c        2 weeks ago         202MB
```

4.  以此类推，每开发一个版本打一个标签，如果以后我想回滚版本，就可以使用指定标签的镜像来创建容器：

```docker
[root@localhost ~]$ docker run -itd centos:v1
```

