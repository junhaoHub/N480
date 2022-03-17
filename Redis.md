# MySql
## 缺点
90年代，一个网站的访问量一般不会太大，单个数据库完全够用。随着用户增多，网站出现以下问题

	数据量增加到一定程度,单机数据库就放不下了
	数据的索引(B+ Tree),一个机器内存也存放不下
	访问量变大后（读写混合）,一台服务器承受不住

![image-20220317090057981](E:\Typora\Redis\image-20220317090057981.png)

## 架构演进
### Memcached + Mysql + 读写分离

![image-20220317090954079](E:\Typora\Redis\image-20220317090954079.png)

	网站80%的情况都是在读，每次都要去查询数据库的话就十分的麻烦！所以说我们希望减轻数据库的压力，我们可以使用缓存来保证效率


### 分库分表 + Mysql集群 + 水平拆分
本质是数据库的读写

![image-20220317091827143](E:\Typora\Redis\image-20220317091827143.png)

## 互联网项目

![image-20220317095111141](E:\Typora\Redis\image-20220317095111141.png)

## 缓存优化

	优化数据库的数据结构和索引(难度大)
	文件缓存，通过IO流获取比每次都访问数据库效率略高
	MemCach，当时最热门的技术，通过在数据库和数据库访问层之间加上一层缓存，第一次访问时查询数据库，将结果保存到缓存，后续的查询先检查缓存，若有直接拿去使用，效率显著提升。

# NoSql
## 概述

NoSQL=Not Only SQL,泛指非关系型数据库，关系型数据库无法满足大量数据要求。Nosql数据库就能轻松解决。
~~~
关系型数据库：列+行,同一个表下数据的结构是一样的。
非关系型数据库：数据存储没有固定的格式,并且可以进行横向扩展。

NoSQL泛指非关系型数据库,随着web2.0互联网的诞生,传统的关系型数据库很难对付web2.0时代！尤其是超大规模的高并发的社区，暴露出来很多难以克服的问题，NoSQL在当今大数据环境下发展的十分迅速，Redis是发展最快的。
~~~

##  特点
~~~
解耦
方便扩展（数据之间没有关系,很好扩展！）
大数据量高性能（Redis一秒可以写8万次,读11万次,NoSQL的缓存记录级,是一种细粒度的缓存,性能会比较高！）
数据类型是多样型的！（不需要事先设计数据库,随取随用）
~~~

## 关系型数据库 VS NoSQL
```
传统的 RDBMS(关系型数据库)
- 结构化组织
- SQL
- 数据和关系都存在单独的表中 row col
- 操作，数据定义语言
- 严格的一致性
- 基础的事务
- ...
```

```
Nosql
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库（社交关系）
- 最终一致性
- CAP定理和BASE
- 高性能,高可用,高扩展
- ...
```
## 3V 3高
大数据时代的3V ：主要是**描述问题**的

1. 海量Velume
2. 多样Variety
3. 实时Velocity

大数据时代的3高 ： 主要是**对程序的要求**

1. 高并发
2. 高可扩
3. 高性能

## 混合使用
真正在公司中的实践：NoSQL + RDBMS 一起使用才是最强的
```bash
# 商品信息
- 一般存放在关系型数据库：Mysql,阿里巴巴使用的Mysql都是经过内部改动的。

# 商品描述、评论(文字居多)
- 文档型数据库：MongoDB

# 图片
- 分布式文件系统 FastDFS
- 淘宝：TFS
- Google: GFS
- Hadoop: HDFS
- 阿里云: oss

# 商品关键字 用于搜索
- 搜索引擎：solr,elasticsearch
- 阿里：Isearch 多隆

# 商品热门的波段信息
- 内存数据库：Redis，Memcache

# 商品交易，外部支付接口
- 第三方应用
```

# 四大分类
## KV键值对

- 新浪：**Redis**
- 美团：Redis + Tair
- 阿里、百度：Redis + Memcache

## 文档型数据库（json数据格式）

- **MongoDB**(掌握)
  - 基于分布式文件存储的数据库。C++编写，用于处理大量文档。
  - MongoDB是RDBMS和NoSQL的中间产品。MongoDB是非关系型数据库中功能最丰富的，NoSQL中最像关系型数据库的数据库。
- ConthDB

## 列存储数据库

- **HBase**(大数据必学)
- 分布式文件系统

> **图关系数据库**

用于广告推荐，社交网络

- **Neo4j**、InfoGrid

| **分类**                | **Examples举例**                                   | **典型应用场景**                                             | **数据模型**                                    | **优点**                                                     | **缺点**                                                     |
| ----------------------- | -------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **键值对（key-value）** | Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。 | Key 指向 Value 的键值对，通常用hash table来实现 | 查找速度快                                                   | 数据无结构化，通常只被当作字符串或者二进制数据               |
| **列存储数据库**        | Cassandra, HBase, Riak                             | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存在一起              | 查找速度快，可扩展性强，更容易进行分布式扩展                 | 功能相对局限                                                 |
| **文档型数据库**        | CouchDB, MongoDb                                   | Web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解Value的内容） | Key-Value对应的键值对，Value为结构化数据        | 数据结构要求不严格，表结构可变，不需要像关系型数据库一样需要预先定义表结构 | 查询性能不高，而且缺乏统一的查询语法。                       |
| **图形(Graph)数据库**   | Neo4J, InfoGrid, Infinite Graph                    | 社交网络，推荐系统等。专注于构建关系图谱                     | 图结构                                          | 利用图结构相关算法。比如最短路径寻址，N度关系查找等          | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群 |



# CAP
# BASE
# Redis入门
# Redis安装
## Windows
## Linux
# 五大基本数据类型
## String
## List
## Set
## Hash
## Zset
# 三种特殊数据类型
## Geo
## hyperlog
## bitmap
# 配置文件
# 持久化
## RDB
## AOF
# 事务操作
## 订阅发布
## 主从复制
## 哨兵模式
## 缓存穿透
## 缓存雪崩
## Jedis
## SpringBoot
## 实践分析
