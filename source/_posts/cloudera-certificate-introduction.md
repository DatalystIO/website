---
title: Cloudera认证简介
date: 2016/11/13
---

### 简介
在大数据越来越火的当下, 很多人都希望进入前景良好的大数据行业. Cloudera做为 Hadoop 商业环境中最具有号召力的企业之一, 自身推出的一系列 Hadoop 相关的认证也在业界享有很高的声誉.

目前, Cloudera 的认证体系下有三种证书:

- Cloudera认证专家: 数据科学家认证 (Cloudera Certificated Profession of Data Scientist). 以后简称 CCPDS.
- Cloudera认证专家: 数据工程师认证 (Cloudera Certificated Profession of Data Engineer). 以后简称 CCPDE.
- Cloudera认证助理: Spark 和 Hadoop 开发者认证 (Cloudera Certificated Associate of Spark and Hadoop Developer). 以后简称CCASH.

三种证书之间没有层级和必要关系, 可根据个人喜好进行选择. 对于这三种证书, Cloudera 官网都列出了详细要求.

### CCPDS

### CCPDE

### CCASH
对于 CCASH 证书主要有以下三个方面的要求:

- 数据导入
- 数据变换和存储
- 数据分析

#### 数据导入
数据导入相关技能主要是指外部系统和集群之间的数据转移, 具体为:
- 使用 **Sqoop** 将 MySQL 数据库中的数据导入 HDFS
- 使用 **Sqoop** 将 HDFS 中的数据导入 MySQL 数据库
- 导入数据时使用 **Sqoop** 修改数据分隔符和文件格式
- 使用 **Flume** 向 HDFS 导入 实时 和 近实时 数据流
- 使用 Hadoop 文件系统命令 导入和导出 HDFS 数据

#### 数据变换, Stage和存储
转换在 HDFS 中已有数据的格式并写回 HDFS. 这包含使用 **Scala** 和 **Python** 编写 **Spark** 程序. 具体指:
- 使用 **Spark** 读取 **HDFS** 中的数据并写回
- 使用 **Spark** 连接两个不同的数据集
- 使用 **Spark** 进行数据聚合, 计算基本统计信息
- 使用 **Spark** 过滤数据集
- 使用 **Spark** 编写查询语句计算商品排名或对数据进行排序

#### 数据分析
使用数据定义语言(DDL) 在 Hive 和 Impala 中创建数据表, 具体为:
- 使用 **Hive** 根据已知图式 读取和创建表格
- 使用 **avro-tools** 从数据文件中抽取 **Avro** 图式
- 使用 **Avro** 文件格式和外部图式文件在 **Hive** 中创建表格
- 使用 **Hive** 创建分区表以提高查询性能
- 通过修改 **JSON** 文件更改 **Avro** 图式


### 总结

如果你自己观察各个证书的考试大纲就会发现:

1. 数据科学家认证侧重于数据分析和数学建模
2. 数据工程师和 Hadoop 开发者认证更侧重于工程实现
3. 各个证书之间重叠的知识点比较多




接下来的一段时间我将会用一系列文章介绍如何备考 **Cloudera Hadoop 和 Spark 开发者认证 (CCASH)**.
