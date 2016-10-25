---
title: MySQL-索引与存储引擎
date: 2016/10/25
---

# MySQL-索引与存储引擎

## 索引简介

每张表对索引的大小和数量上都是有一定的限制，不同的存储引擎(Myisam，Innodb）都是不一样的，但有一个共性是支持至少16个索引，总的索引长度256字节。


### 前缀索引(Prefix Indexes)
当你设置了一个字段为TEXT或者BLOB的类型时，怎么对这个对这个字段做索引呢？要知道这两种类型存储的数据是极大的，要是允许创建所以，你的索引文件就要爆炸了；当然当你尝试去在这上面建立索引时，Mysql是会给你报错了，提示你不允许在它们上面建立索引。这时候你可以使用前缀索引，如字面意思，就是提取某个字段的前缀的一部分串来建立索引，格式：col_name(N)-N指代前缀的长度。
以下是范例：

```
CREATE TABLE test (blob_col BLOB, INDEX(blob_col(10)));
```
注意：前缀索引是可以达到1000字节长度，当然如果是Innodb，那么它只支持767字节，除非你使用innodb_large_prefix来设置。

还需要额外注意的是，当前缀索引建立在非二进制类型上(CHAR, VARCHAR, TEXT)，那么该索引的长度限制是以字符(character)来衡量的；建立在二进制类型上(BINARY, VARBINARY, BLOB)，那么该索引的长度限制是以字节(byte)来衡量的。
### 全文索引(FULLTEXT Indexes)
全文索引是一种非常适合全文检索的索引方式，只在MyISAM引擎支持，并且只能是CHAR、VARCHAR、TEXT这三类的字段上，不支持前缀方式，必须是整个字段。（具体全文索引可以参考附录4来详细了解）

### 空间索引(Spatial Indexes)
空间索引是只能创建在空间类型的字段上(spatial data types)的，MyISAM引擎是采用R-Tree结构来实现的，其他引擎采用的是B-Tree，当然要排除ARCHIVE引擎，它不支持该索引。


## 存储引擎

### 简介
Mysql支持的数据库存储引擎有哪些?（通过输入**SHOW ENGINES**可以查看当前Mysql Server支持的版本，Yes代表支持，No代表不支持，Default代表是当前支持并默认的）

```
1.InnoDB-5.5.5及其以后版本的默认引擎,是事务安全型的存储引擎，支持提交(commit)、回滚(rollback)、崩溃回复(crash-recovery)，采用行级别的锁，并采用Oracle风格的非锁方式的一致性读方案，来应对多用户的并发访问。同时将用户数据存储在聚集索引(clustered index)中，来减少基于主键的查询请求，降低IO压力。为了维持数据的完整性，该引擎也支持外键关联的参照完整性约束（参照完整性约束-关系中不允许引用不存在的实体）。
2.NDB-Mysql Cluster支持的分布式引擎，也叫NDBCLUSTER，通常用于高可用，高稳定性场景，在标准的Mysq5.5.5版本并不支持，需要包含Mysql Cluster NDB版本7.0或者7.1的Mysql5.1版本，以及包含Mysql Cluster NDB7.2版本的Mysql5.5。
3.MyISAM-通常用于网站、数据仓库环境，是5.5.5以前版本的默认数据库引擎。
4.Memory-存储数据的位置在RAM中，为了快速检索使用，又名为HEAP引擎。
5.Merge-能够用于逻辑分组一堆相同的MyISAM引擎表，作为一个对象使用，非常适合数据仓库中的超大型数据环境(VLDB)使用。
6.Archive-非常适合存储和检索大量很少引用的历史数据、归档数据、安全审计数据。
7.Federated-能够将多个不同物理主机上的独立的Mysql Servers服务连接起来，形成一个逻辑数据库，非常适合分布式或者数据集市场景。
8.CSV-该引擎使用文本文件存储数据，并用,作为分割符，可以用来很方便的处理从其他软件到处的cvs格式数据。
9.Blackhole-该引擎不存储任何数据，检索的时候总是返回空集合，这个功能通常被用在分布式数据库设计中，用于那些数据自动复制，但却不需要本地存储的场景。
10.Example-也叫stub引擎，也是不用来存储和检索数据的，主要用于Mysql源码演示如何写一个新的存储引擎的，没啥卵用。
```

<table class="table" summary="Storage Engines Feature Summary" border="1"><colgroup><col class="feature"><col class="myisam"><col class="memory"><col class="innodb"><col class="archive"><col class="ndb"></colgroup><thead><tr><th scope="col">Feature</th><th scope="col">MyISAM</th><th scope="col">Memory</th><th scope="col">InnoDB</th><th scope="col">Archive</th><th scope="col">NDB</th></tr></thead><tbody><tr><td scope="row">Storage limits</td><td>256TB</td><td>RAM</td><td>64TB</td><td>None</td><td>384EB</td></tr><tr><td scope="row">Transactions</td><td>No</td><td>No</td><td>Yes</td><td>No</td><td>Yes</td></tr><tr><td scope="row">Locking granularity</td><td>Table</td><td>Table</td><td>Row</td><td>Row</td><td>Row</td></tr><tr><td scope="row">MVCC</td><td>No</td><td>No</td><td>Yes</td><td>No</td><td>No</td></tr><tr><td scope="row">Geospatial data type support</td><td>Yes</td><td>No</td><td>Yes</td><td>Yes</td><td>Yes</td></tr><tr><td scope="row">Geospatial indexing support</td><td>Yes</td><td>No</td><td>Yes<a href="#ftn.idm139813344539376" class="footnote" name="idm139813344539376"><sup class="footnote">[a]</sup></a></td><td>No</td><td>No</td></tr><tr><td scope="row">B-tree indexes</td><td>Yes</td><td>Yes</td><td>Yes</td><td>No</td><td>No</td></tr><tr><td scope="row">T-tree indexes</td><td>No</td><td>No</td><td>No</td><td>No</td><td>Yes</td></tr><tr><td scope="row">Hash indexes</td><td>No</td><td>Yes</td><td>No<a href="#ftn.idm139813344531504" class="footnote" name="idm139813344531504"><sup class="footnote">[b]</sup></a></td><td>No</td><td>Yes</td></tr><tr><td scope="row">Full-text search indexes</td><td>Yes</td><td>No</td><td>Yes<a href="#ftn.idm139813344528544" class="footnote" name="idm139813344528544"><sup class="footnote">[c]</sup></a></td><td>No</td><td>No</td></tr><tr><td scope="row">Clustered indexes</td><td>No</td><td>No</td><td>Yes</td><td>No</td><td>No</td></tr><tr><td scope="row">Data caches</td><td>No</td><td>N/A</td><td>Yes</td><td>No</td><td>Yes</td></tr><tr><td scope="row">Index caches</td><td>Yes</td><td>N/A</td><td>Yes</td><td>No</td><td>Yes</td></tr><tr><td scope="row">Compressed data</td><td>Yes<a href="#ftn.idm139813344518976" class="footnote" name="idm139813344518976"><sup class="footnote">[d]</sup></a></td><td>No</td><td>Yes<a href="#ftn.idm139813344517664" class="footnote" name="idm139813344517664"><sup class="footnote">[e]</sup></a></td><td>Yes</td><td>No</td></tr><tr><td scope="row">Encrypted data<a href="#ftn.idm139813344515888" class="footnote" name="idm139813344515888"><sup class="footnote">[f]</sup></a></td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr><tr><td scope="row">Cluster database support</td><td>No</td><td>No</td><td>No</td><td>No</td><td>Yes</td></tr><tr><td scope="row">Replication support<a href="#ftn.idm139813344510416" class="footnote" name="idm139813344510416"><sup class="footnote">[g]</sup></a></td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr><tr><td scope="row">Foreign key support</td><td>No</td><td>No</td><td>Yes</td><td>No</td><td>No</td></tr><tr><td scope="row">Backup / point-in-time recovery<a href="#ftn.idm139813344505008" class="footnote" name="idm139813344505008"><sup class="footnote">[h]</sup></a></td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr><tr><td scope="row">Query cache support</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr><tr><td scope="row">Update statistics for data dictionary</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td><td>Yes</td></tr></tbody><tbody class="footnotes"><tr><td colspan="6">
<div id="ftn.idm139813344539376" class="footnote">
<p><a href="#idm139813344539376" class="para"><sup class="para">[a] </sup></a>InnoDB support for geospatial indexing is available in MySQL 5.7.5 and higher.</p>
</div>

<div id="ftn.idm139813344531504" class="footnote">
<p><a href="#idm139813344531504" class="para"><sup class="para">[b] </sup></a>InnoDB utilizes hash indexes internally for its Adaptive Hash Index feature.</p>
</div>

<div id="ftn.idm139813344528544" class="footnote">
<p><a href="#idm139813344528544" class="para"><sup class="para">[c] </sup></a>InnoDB support for FULLTEXT indexes is available in MySQL 5.6.4 and higher.</p>
</div>

<div id="ftn.idm139813344518976" class="footnote">
<p><a href="#idm139813344518976" class="para"><sup class="para">[d] </sup></a>Compressed MyISAM tables are supported only when using the compressed row format. Tables using the compressed row format with MyISAM are read only.</p>
</div>

<div id="ftn.idm139813344517664" class="footnote">
<p><a href="#idm139813344517664" class="para"><sup class="para">[e] </sup></a>Compressed InnoDB tables require the InnoDB Barracuda file format.</p>
</div>

<div id="ftn.idm139813344515888" class="footnote">
<p><a href="#idm139813344515888" class="para"><sup class="para">[f] </sup></a>Implemented in the server (via encryption functions). Data-at-rest tablespace encryption is available in MySQL 5.7 and higher.</p>
</div>

<div id="ftn.idm139813344510416" class="footnote">
<p><a href="#idm139813344510416" class="para"><sup class="para">[g] </sup></a>Implemented in the server, rather than in the storage engine.</p>
</div>

<div id="ftn.idm139813344505008" class="footnote">
<p><a href="#idm139813344505008" class="para"><sup class="para">[h] </sup></a>Implemented in the server, rather than in the storage engine.</p>
</div>
</td></tr></tbody></table>

## 附录
1.【Column Indexes】https://dev.mysql.com/doc/refman/5.5/en/column-indexes.html

2.【Alternative Storage Engines】https://dev.mysql.com/doc/refman/5.5/en/storage-engines.html

3.【The InnoDB Storage Engine】https://dev.mysql.com/doc/refman/5.5/en/innodb-storage-engine.html

4.【Full-Text Search Functions】https://dev.mysql.com/doc/refman/5.5/en/fulltext-search.html