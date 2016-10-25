---
title: Centos安装Cloudera CDH（YUM方式）
date: 2016/10/24
---

#Centos安装Cloudera CDH（YUM方式）

##简介
Cloudera提供使用Yum方式或者下载RPM包进行安装，在接下来的环节，将采用Yum方式安装Cloudera。

##编辑Yum的Repo
Cloudera的Yum安装地址：https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/cloudera-cdh5.repo?_ga=1.50190628.383672037.1471754216 。

在/etc/yum.repos.d目录下新建文件“cloudera-cdh5.repo”

```
[root@e38f507299b0 yum.repos.d]#vi /etc/yum.repos.d/cloudera-cdh5.repo
[cloudera-cdh5]
# Packages for Cloudera's Distribution for Hadoop, Version 5, on RedHat	or CentOS 6 x86_64
name=Cloudera's Distribution for Hadoop, Version 5
baseurl=https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/5/
gpgkey =https://archive.cloudera.com/cdh5/redhat/6/x86_64/cdh/RPM-GPG-KEY-cloudera    
gpgcheck = 1
```

##安装步骤（安装CDH5的YARN版本）
1.安装Resource Manager host

```
sudo yum clean all
sudo yum install hadoop-yarn-resourcemanager
```

2.安装NameNode

```
sudo yum clean all
sudo yum install hadoop-hdfs-namenode
```
3.安装Secondary NameNode

```
sudo yum clean all
sudo yum install hadoop-hdfs-secondarynamenode
```
4.安装DataNode/Yarn/MapReduce（所有节点需要执行，除了安装了Resource Manager的节点）

```
sudo yum clean all
sudo yum install hadoop-yarn-nodemanager hadoop-hdfs-datanode hadoop-mapreduce
```

5.安装Proxy Server/History Server（选取集群中的一个节点即可）

```
sudo yum clean all
sudo yum install hadoop-mapreduce-historyserver hadoop-yarn-proxyserver
```

6.安装Hadoop客户端

```
sudo yum clean all
sudo yum install hadoop-client
```

##参考来源
1.【Cloudera CDH下载】http://www.cloudera.com/downloads/cdh/5-8-2.html
2.【Cloudera企业版文档】http://www.cloudera.com/content/dam/www/static/documents/datasheets/cloudera-enterprise-datasheet.pdf
3.【Cloudera安装文档】http://www.cloudera.com/documentation/enterprise/latest/topics/cdh_ig_cdh5_install.html#topic_4_4_1__p_32