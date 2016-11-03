---
title: Docker安装Centos
date: 2016/10/24
---

#Docker安装Centos

##设置Docker国内镜像
镜像源地址可以通过阿里云申请一个：https://dev.aliyun.com/search.html ，也可以去网易镜像库寻找资源https://c.163.com/hub#/m/home/ 。

##安装Centos
```
docker pull centos
```
##启动Centos
```
docker run -d -i -t centos /bin/bash
```
##进入Centos
查看运行后的容器ID

```
docker ps
```
<pre>
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e38f507299b0        centos              "/bin/bash"         33 minutes ago      Up 1 seconds                            naughty_curie
</pre>

找到容器ID，通过命令

```
docker attach e38f507299b0
```
进入容器中，这就是centos的一个系统在docker中运行

<pre>
[root@e38f507299b0 /]# ifconfig
bash: ifconfig: command not found
</pre>

##安装Centos中ifconfig命令
竟然没有ifconfig命令，接着使用你会发现ssh也没有，还有我常用的命令which，来吧，我们开始安装这些组件；这已经是一个centos系统了，那么就让我们用yum开始安装吧。

```
yum install net-tools.x86_64
```

##安装Which命令

```
yum install which
```

##安装SSH
服务端SSH服务

```
yum install openssh-server  ##安装SSH服务
sshd -D &  ##启动SSH服务
```

SSH客户端工具

```
yum install openssh-clients
```
