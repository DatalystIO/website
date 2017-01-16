---
title: Learning Sqoop By Practice
date: 2017/01/04
---


[TOC]

## Introductin
`Sqoop` is a tool designed to transfer data between `Hadoop` and `relational database` servers. 

![Sqoop Works](./1483566758991.png)

### Sqoop Import
> The `sqoop import` tool imports individual table from RDBMS to HDFS. Each row in a table is treated as a record in HDFS. All records are stored as text data in text files or as binary data in Avro and Sequence files. The default format is **text data**.


### Sqoop Export
> The `sqoop export` tool exports a set of files from HDFS back to an RDBMS. The files given as input to Sqoop contain records, which are called as rows in table. Those are read and parsed into a set of records and delimited with user-specified delimiter.


### Sqoop Job
> `sqoop job` creates and saves the import and export commands. It specifies parameters to identify and recall the saved job.


### Sqoop Installation

To see detailed environment setup steps, one can check the following link: [Big Data Experiment Environment Setup]()



## Common Scenarios

> NOTE: All the following problem scenarios are based on `Cloudera QuickStart VM v5.7` and all the solutions can be reproduced using the aforementioned environment.

### Sqoop Import

#### 1. import a table to HDFS

> ps1,

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  -m 1
```

NOTE:
1. `--connect`, `--username`, `--password` are database connection variables for connecting to target RDBMS. In this case, we use MySQL as our data source.
2. `--table` is the specific table that we want to import to HDFS
3. `-m 1`, or `--num-mappers 1` is the parameters of map tasks to use to perform.  By default, **four map tasks are used**.


#### 2. import a MySQL table to a specific directory in HDFS
> ps1,

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps1/products" \
  -m 1
```

NOTE:
1. `--target-dir` is to specify a particular directory in HDFS. `--warehouse-dir` is to specify a particular in Hive. Using `--target-dir` can also point to external table.


#### 3. import a MySQL table to a particular database and table in Hive
> ps16,

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --hive-import \
  --hive-home "/user/hive/warehouse/cca175/ps16/cca175.db" \
  --hive-database cca175 \
  --hive-table products \
  --create-hive-table \
  -m 1
```

NOTE:
1. using `--hive-import`, `--hive-database`, `--hive-table`,  options can ingest data to HDFS and shows in Hive in particular database and particular table. If Hive database doesn't have the target table, we can use `--create-hive-table` to create a new table when sqoop importing data; otherwise we can use `--hive-overwrite` to overwrite the existing data if the target table exisits.




#### 4. import a subset of a MySQL table to HDFS
> ps3, ps4,

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --where "product_id > 10" \
  -m 1
```

NOTE:
1. using `--where condition` to filter out the subset of a table and ingest to HDFS


#### 5. import specific columns of a MySQL table to HDFS
> ps5,

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --columns product_id, product_price \
  -m 1
```

NOTE:
1. we can use `--columns column_name_1, column_name_2, ...` option to select particular columns to ingest to HDFS


#### 6. import a non-primary-key MySQL table to HDFS

> ps

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --split-by product_id \
  -m 1
```

NOTE:
1. sqoop do need table's primary key to balance separate map tasks. If a table don't have any primary key, we have to use `--split-by column_name` variable to explicitly tell sqoop to use which column


#### 7. import a query result to HDFS

> ps14,

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --query "select * from orders o join order_items oi on oi.order_item_order_id=o.order_id where \$CONDITIONS" \
  --split-by order_id \
  --target-dir "/cca175/ps14/order_join" \
  -m 1
```

NOTE:
1. using `--query query_string` to write our query command and sqoop will execute that command before importing data to HDFS. ** where \$ CONDITIONS **  MUST ADD to query_string, otherwise there will be an exception
2. It's better to use `--split-by` option when using `--query` option because the query result don't have any primary key and sqoop will be confused when balancing map tasks.


#### 8. import a MySQL table to HDFS and change delimiter

> ps15,


```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps15/products" \
  --fields-terminated-by "|" \
  --lines-terminated-by "\n" \
  -m 1
```

NOTE:
1.  we can use `--fields-terminated-by` option to specify the `field delimiter` we want use. Similarly, we can use `--lines-terminated-by` to specify the `line delimiter`.


#### 9. import a MySQL table to an existing directory in HDFS in overwrite mode
> ps15

```bash
hdfs dfs -rm -r /cca175/ps15/products;

sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps15/products" \
  -m 1
```

NOTE:
1. if the target directory already exists in HDFS  and we want to `overwrite` the data, we need to first delete the folder and then ingest data using sqoop

#### 10. import a MySQL table to an existing directory in HDFS in append mode
> ps15

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps15/products" \
  --append \
  -m 1
```

NOTE:
1. if the target directory already exists in HDFS and we just want to append imported data to old data, we can simply use `--append` option to achieve that target.


#### 11. import only new data of a MySQL table to HDFS
> ps17

```bash
sqoop import
  --connect "jdbc:mysql://quickstart:3306/retail_db"
  --username retail_dba
  --password cloudera
  --table departments
  --hive-import
  --hive-database cca175
  --hive-table departments_incremental
  --append
  --incremental append
  --check-column department_id
  --last-value 7
```

NOTE:
1. First, we need to setup `--incremental append` to tell sqoop that we want to incremental import. Then we need to specify `--check-column department_id` and `--last-value 7` to tell sqoop the column we want to check and the last imported value of that column



#### 12. import a MySQL table as sequence file in HDFS
> ps26

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps26/products" \
  --as-sequencefile \
  -m 1
```

NOTE:
1. using `--as-sequencefile` to save ingested data as **Sequence File** in HDFS



#### 13. import a MySQL table as parquet file in HDFS
> ps26

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps26/products" \
  --as-parquetfile \
  -m 1
```

NOTE:
1. using `--as-parquetfile` to save ingested data as **Parquet File** in HDFS


#### 14. import a MySQL table as avro file in HDFS
> ps26

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps26/products" \
  --as-avrodatafile \
  -m 1
```

NOTE:
1. using `--as-avrodatafile` to save ingested data as **Avro Data File** in HDFS


#### 15. import a MySQL table to Hive and replace all null values
> ps24,

```bash
sqoop import\
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --hive-import \
  --hive-home "/hive/warehouse/cca175/ps24/products" \
  --hive-database cca175 \
  --hive-table products \
  --hive-overwrite \
  --fields-terminated-by "\001" \
  --null-string "" \
  --null-non-string -999 \
  -m 1
```

NOTE:
1. we can use `--null-string` option to replace all null string to the character we want.
2. similarly, `--null-non-string` option is for replacing non-string null values to the expected character.


#### 16. import a MySQL table to HDFS and change delimiter characters
> ps21

```bash
sqoop import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps21/products/" \
  --enclosed-by "\'" \
  --escaped-by "\\" \
  --fields-terminated-by "~" \
  --lines-terminated-by ":" \
  -m 1
```

NOTE:
1. `--enclosed-by` to set up enclosed character. `--escaped-by` to set up the escaped character.






### Sqoop Export

#### 1. export a Hive table to MySQL
> ps19,

```bash
sqoop export
  --connect "jdbc:mysql://quickstart:3306/retail_db"
  --username retail_dba
  --password cloudera
  --table products_export
  --export-dir "/user/hive/warehouse/cca175/ps19/products_export"
  --batch
```

NOTE:
1. The `--table` and `--export-dir` variables are required for **sqoop export**. These specify the table to populate in the database, and the directory in HDFS that contains the source data.


#### 2. export a Hive table and only update data in MySQL table
> ps20,

```bash
sqoop export \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table departments \
  --export-dir "/user/hive/warehouse/cca175/ps20/departments_new" \
  --update-mode updateonly \
  --update-key department_id \
  -m 5
```

NOTE:
1. we can use `--update-mode updateonly` option to specify that we just want to **update existing records** and we don't want insert any new data to the MySQL table. `--update-key column_name` variable is to specify the particular column_name that we use for updating existing data


#### 3. export a Hive table and insert and update data in MySQL table
> ps20,

```bash
sqoop export \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table departments \
  --export-dir "/user/hive/warehouse/cca175/ps20/departments_new" \
  --update-mode allowinsert \
  --update-key department_id \
  -m 5
```

NOTE
1. Different with previous scenario, now we need to use `--update-mode allowinsert` to tell sqoop that we are not only want to update existing data, but inserting new data to the MySQL table





#### 4. export a Hive table and replace null characters
> ps25,


```bash
sqoop export \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --export-dir "/user/hive/warehouse/cca175/" \
  --input-fields-terminated-by "\001" \
  --input-lines-terminated-by "\n" \
  --input-null-string "" \
  --input-null-non-string -999 \
  -m 1
```

NOTE:
1. we can use `--input-fields-terminated-by` option to setup the input data delimiter in Hive. The default fields delimiter in Hive is `\001`. Similarly, `--input-lines-terminated-by` option is to setup the lines delimiter and the Hive default value is "\n".
2. `--input-null-string` option is to replace all the null string-typed value to the character we want. Whilst, `--input-null-non-string` option is to replace all the null non-string-typed value.





### Sqoop Job

#### 1. create a sqoop job that import a MySQL table to HDFS
> ps27,

```bash
sqoop job \
  --create sqoop_import_products \
  -- import \
  --connect "jdbc:mysql://quickstart:3306/retail_db" \
  --username retail_dba \
  --password cloudera \
  --table products \
  --target-dir "/cca175/ps27/products/" \
  --fields-terminated-by "|" \
  --lines-terminated-by "\n" \
  -m 5
```

NOTE:
1. there is a blank before the import keyword in `-- import`.  `--import` (no blank before import) will not work!!!



























## Resource
- [TutorialsPoint: Learn Sqoop](http://www.tutorialspoint.com/sqoop/)
- [Stackoverflow: Using Sqoop to Import Data from MySQL to Hive](http://stackoverflow.com/questions/22404641/using-sqoop-to-import-data-from-mysql-to-hive)
- [Sqoop User Guide v1.4.2](https://sqoop.apache.org/docs/1.4.2/SqoopUserGuide.html)
