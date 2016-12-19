---
title: CCA Preparation - Sqoop
date: 2016/12/11
---

### Introduction

What is Sqoop?

> Sqoop is a tool designed to transfer data between `Hadoop` and `relational databases` or `mainframes`. 

What can I do with Sqoop?

> You can use Sqoop to import data from a relational database management system (RDBMS) such as MySQL or Oracle or a mainframe into the Hadoop Distributed File System (HDFS), transform the data in Hadoop MapReduce, and then export the data back into an RDBMS.

What else can Sqoop do?

> Sqoop automates most of this process, relying on the database to describe the schema for the data to be imported. Sqoop uses MapReduce to import and export the data, which provides parallel operation as well as fault tolerance.

### CCA Related Sqoop knowledge
According to [CCA syllabus](http://www.cloudera.com/training/certification/cca-spark.html), below are the core part that will be examed in CCA exam.

- Import data from a MySQL database into HDFS using Sqoop
- Export data to a MySQL database from HDFS using Sqoop
- Change the delimiter and file format of data during import using Sqoop




### Sqoop Common Commands


#### list all database
The `list-databases` will list database schemas present on a server.
```bash
sqoop list-databases \
      --connect "jdbc:mysql://quickstart.cloudera:3306" \
      --username retail_dba \
      --password cloudera
```

#### list all tables in a database
The `list-tables` will list tables schemas present on a database
```bash
sqoop list-tables \
      --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
      --username retail_dba \
      --password cloudera
```

#### eval a query
The `eval` tool allows user to quickly run a simple SQL query against a database.
```bash
sqoop eval  \
      --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
      --username retail_dba \
      --password cloudera \
      --query "select * from departments"
```


#### import all tables
The `import-all-tables` tool imports a set of tables from an RDBMS to HDFS. Data from each table is stored in a separate directory in HDFS.

For example,

```bash
sqoop import-all-tables \
      -m 12 \
      --connect "jdbc:mysql://quickstart.cloudera:3306/retail_db" \
      --username retail_dba \
      --password cloudera \
      --warehouse-dir=/user/hive/warehouse/retail_stage.db
```

the following code will just **import all tables in retail_db to HDFS with 12 map reduce tasks, while not showing in Hive** .



























### Reference
- [Sqoop User Guide](http://archive-primary.cloudera.com/cdh5/cdh/5/sqoop-1.4.5-cdh5.3.2/SqoopUserGuide.html)
- [Sample Code for Practise Sqoop](https://github.com/DatalystIO/code/cca175/sqoop_example.sh)

