---
title: 利用docker部署hadoop集群的记录
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-22 23:03:21
subtitle:
tags:
  -	DOCKER
categories:
  -	docker
---

**计划部署五个docker容器，node1 为主节点，部署hdfs的namenode，yarn的resourcemager，redis的master等节点，目的就是拿docker当虚拟机用。**

**理想很丰满，现实很骨干，问题重重,现把遇到的问题记录下，做相应的知识补充，也防止下次再次掉坑。**

启动容器：

1. 启动就退出，需要让容器内启动一个进程，否则没有服务 就退掉了

    ```shell
    docker exec -it /bin/bash
    ```

2. 拿docker容器部署多个服务，所以只能用最基础的centos 镜像，docker容器内的内核是受限制的，默认不能使用sytemctl命令，启动时候，要允许使用特权

 ```shell
 报错:
     [root@4decd9c3c3aa /]# systemctl start sshd
     Failed to get D-Bus connection: Operation not permitted
 报错： 
   System has not been booted with systemd as init system (PID 1). Can't operate.    
   Failed to connect to bus: Host is down
 解决：
  docker run -itd   --privileged --name myCentos centos /usr/sbin/init
 ```

3.docker 部署的多个容器肯定是要通信的，可以直接建立一个桥接网络，启动容器时候，加入到网络中

```shell
  docker run -itd --privileged --name t4 --network docker_net hub.c.163.com/library/centos  /usr/sbin/init bash
```



4.节点间肯定是要传数据的，可以使用docker cp 实现宿主机和容器的互传，使用scp依赖ssh服务

```shell
安装完以后报如下的错:
    The authenticity of host 'oradb24 (192.168.1.24)' can't be established.
	RSA key fingerprint is e0:fb:fb:67:a0:fc:cd:a5:dc:c8:bc:68:67:b1:93:3f.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'oradb24,192.168.1.24' (RSA) to the list of known hosts.
	root@oradb24's password: 
	bash: scp: command not found
	lost connection
注意，拷贝文件的双方服务器都要安装ssh服务，并且服务端和客户端都要安装完整才行
yum install -y openssh-server
yum install -y openssh-clients
systemctl restart sshd #启动ssh服务
```

5. 一些常用的命令，直接yum安装不行，不是直接的命令，是在一个包下，会提示找不到

   ```shell
   netstat、telnet 命令
       yum install net-tools
   pstree 命令
       yum install psmisc -y
       
   有时候不一定什么样，学会搜学，学会通用的本领
   yum list | grep telnet
   ```

6. scp装完之后，想要用的话，是需要登陆账户的，默认root账户密码，你自己是不知道的，修改文件太麻烦，所以，注意启动容器第一件事就是先修改root密码

```shell
[root@06e23c44de37 /]# passwd root
Changing password for user root.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
```

7. 在一台节点上，装好所有的服务，做好一个容器，保存为一个镜像，然后，利用这个镜像启动多个容器，注意启动时候的命令

```
docker commit -a "zhangxiaodong" -m "bigdata centos7" 5d8e05402c40  mycentos:v1
```

8.  防止出现意外，把镜像提交到docker.hub仓库中去

```shell
一：网易镜像仓库，但是现在需要企业认证才让推，坑爹
二：官方的hub.docker
    docker tag masternode:v1  12345678900100/masternode:v1  #这一步是重新标记镜像，得按照 注册用户名/镜像名  这个规范来才能推送上去
```



