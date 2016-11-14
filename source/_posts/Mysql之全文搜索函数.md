---
title: Mysql之全文搜索函数
date: 2016/10/25
---

# Mysql之全文搜索函数(TODO)

MATCH (col1,col2,...) AGAINST (expr [search_modifier])

## 简介

```
search_modifier:
  {
       IN NATURAL LANGUAGE MODE
     | IN NATURAL LANGUAGE MODE WITH QUERY EXPANSION
     | IN BOOLEAN MODE
     | WITH QUERY EXPANSION
  }
```

Mysql支持全文索引和检索，有如下几个特征：

+ 全文索引类型是FULLTEXT
+ 全文索引只能被用在MyISAM引擎的表中（如果Mysql版本>=5.6，全文索引也可以被用在InnoDB引擎的表中）
+ 全文索引只能被创建在CHAR、VARCHAR、TEXT类型的字段上
+ 全文索引可以在建表的时候设定，也可以在建表后进行更改
+ 如果一个表数据量很大，那么增加了全文索引，将会导致其加载数据变慢。

## 场景

全文检索使用MATCH(col_name)AGAINST(...)表达格式。MATCH括号中使用逗号分隔罗列需要被检索的字段列表。AGAINST()括号中是需要被检索的内容。

有三种全文检索查询方式:

1.	自然语言检索(A natural language search)：会将检索串进行自然语言分词，拆解成一个个词组，并且会使用停词列表来处理。通常情况，如果一个词出现在超过50%数据里，那么这个词将会被认为是通用词，不启动匹配。（IN NATURAL LANGUAGE MODE）
2. 真值检索(A boolean search )：真值检索使用特殊的检索
语言--AGAINST(string IN BOOLEAN MODE)，当然也能有其他很多种操作方式，比如包含或不包含，大于或者小于。（IN BOOLEAN MODE）
3.	查询表达式检索(A query expansion search)：这是一种修改版的自然语言检索方式，检索字符串时，第一次会使用自然语言检索的结果，然后将检索结果最相关行的单词添加到搜索字符中，再次检索，返回第二次检索结果。（The IN NATURAL LANGUAGE MODE WITH QUERY EXPANSION 或 WITH QUERY EXPANSION）

## 附录

1.【Full-Text Search Functions】https://dev.mysql.com/doc/refman/5.5/en/fulltext-search.html
2.【Natural Language Full-Text Searches】https://dev.mysql.com/doc/refman/5.5/en/fulltext-natural-language.html
3.【Boolean Full-Text Searches】https://dev.mysql.com/doc/refman/5.5/en/fulltext-boolean.html
4.【Full-Text Searches with Query Expansion】https://dev.mysql.com/doc/refman/5.5/en/fulltext-query-expansion.html
