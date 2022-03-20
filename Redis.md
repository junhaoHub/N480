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
- 一般存放在关系型数据库:Mysql,阿里巴巴使用的Mysql都是经过内部改动的。

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

## 文档型数据库（json格式）

- **MongoDB**(掌握)
  - 基于分布式文件存储的数据库。C++编写，用于处理大量文档。
  - MongoDB是RDBMS和NoSQL的中间产品。MongoDB是非关系型数据库中功能最丰富的，NoSQL中最像关系型数据库的数据库。
- ConthDB

## 列存储数据库

- **HBase**(大数据必学)
- 分布式文件系统

## 图关系数据库

用于广告推荐，社交网络

- **Neo4j**、InfoGrid

| **分类**                | **Examples举例**                                   | **典型应用场景**                                             | **数据模型**                                    | **优点**                                                     | **缺点**                                                     |
| ----------------------- | -------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **键值对（key-value）** | Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。 | Key 指向 Value 的键值对，通常用hash table来实现 | 查找速度快                                                   | 数据无结构化，通常只被当作字符串或者二进制数据               |
| **列存储数据库**        | Cassandra, HBase, Riak                             | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存在一起              | 查找速度快，可扩展性强，更容易进行分布式扩展                 | 功能相对局限                                                 |
| **文档型数据库**        | CouchDB, MongoDb                                   | Web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解Value的内容） | Key-Value对应的键值对，Value为结构化数据        | 数据结构要求不严格，表结构可变，不需要像关系型数据库一样需要预先定义表结构 | 查询性能不高，而且缺乏统一的查询语法。                       |
| **图形(Graph)数据库**   | Neo4J, InfoGrid, Infinite Graph                    | 社交网络，推荐系统等。专注于构建关系图谱                     | 图结构                                          | 利用图结构相关算法。比如最短路径寻址，N度关系查找等          | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群 |

# CAP
## Consistency(强一致性)
	系统在执行过某项操作后仍然处于一致的状态。在分布式系统中,更新操作执行成功后所有的用户都应该读到最新的值,这样的系统被认为是具有强一致性的。 等同于所有节点访问同一份最新的数据副本。

## Availability(可用性)
	每一个操作总是能够在一定的时间内返回结果,这里需要注意的是”一定时间内”和”返回结果”。一定时间指的是,在可以容忍的范围内返回结果,结果可以是成功或者失败。

## Partitiontolerance(分区容错性)
	指当出现网络分区的情况时（即系统中的一部分节点无法和其他节点进行通信）分离的系统也能够正常运行。理解为在存在网络分区的情况下,仍然可以接受请求。节点crash或者网络分片都不应该导致一个分布式系统停止服务。
	CAP既适用于NoSQL数据库,也适用于关系型数据库。它是NoSQL数据库、关系型数据库,乃至一切分布式系统在设计数据多个副本之间读写一致性问题时需要遵循的共同原则

# BASE
	OpenOffice.org套件之一,其功能类似于微软Office(TM)里的高端数据库产品。新的 2.0 版本的 BASE 可以使用内部操作数据库中的数据。用户可以使用自己的数据库软件或者 BASE 自己的基于 HSQL 的数据库引擎来创建和修改表格、表单、查询、报告。 BASE 为初、中、高级用户提供了向导、设计视图和 SQL 视图作为选择来设计数据库。
# Redis入门
Redis（Remote Dictionary Server )，即远程字典服务。

	一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、**Key-Value数据库**，并提供多种语言的API。
	与memcached一样,为了保证效率,**数据都是缓存在内存中**。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

## 功能
	内存存储、持久化，内存是断电即失的，所以需要持久化（RDB、AOF）
	高效率、用于高速缓冲
	发布订阅系统
	地图信息分析
	计时器、计数器(eg：浏览量)
	...

## 特性 
	多样的数据类型
	持久化
	集群
	事务


# Redis安装
## Linux安装

1. 下载安装包！`redis-5.0.8.tar.gz`

2. 解压Redis的安装包！程序一般放在 `/opt` 目录下

   ![image-20220318093015516](E:\Typora\Redis\image-20220318093015516.png)

3. 基本环境安装

```bash
yum install gcc-c++
# 然后进入redis目录下执行
make
# 然后执行
make install
```

![image-20220318093124468](E:\Typora\Redis\image-20220318093124468.png)


1. redis默认安装路径 `/usr/local/bin`

   ![image-20220318093158924](E:\Typora\Redis\image-20220318093158924.png)

2. 将redis的配置文件复制到 程序安装目录 `/usr/local/bin/kconfig`下

![image-20220318093210556](E:\Typora\Redis\image-20220318093210556.png)

3. redis默认不是后台启动的，需要修改配置文件！

   ![image-20220318093225584](E:\Typora\Redis\image-20220318093225584.png)


4. 通过制定的配置文件启动redis服务

   ![image-20220318093248181](E:\Typora\Redis\image-20220318093248181.png)


5. 使用redis-cli连接指定的端口号测试，Redis的默认端口6379

   ![image-20220318093311766](E:\Typora\Redis\image-20220318093311766.png)


6. 查看redis进程是否开启

![image-20220318093323661](E:\Typora\Redis\image-20220318093323661.png)

7. 关闭Redis服务 `shutdown`

![image-20220318093334458](E:\Typora\Redis\image-20220318093334458.png)

8. 再次查看进程是否存在

9. 后面我们会使用单机多Redis启动集群测试

## 测试性能

**redis-benchmark：**Redis官方提供的性能测试工具，参数选项如下：

![img](https://img-blog.csdnimg.cn/20200513214125892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MzIyNw==,size_16,color_FFFFFF,t_70)

```bash
# 100个并发连接 100000请求
redis-benchmark -h localhost -p 6379 -c 100 -n 10000012
```

![image-20220318093800234](E:\Typora\Redis\image-20220318093800234.png)

## 基础知识

> redis默认有16个数据库

![image-20220318094310595](E:\Typora\Redis\image-20220318094310595.png)

默认使用的第0个;

16个数据库为：DB 0~DB 15
默认使用DB 0 ，可以使用`select n`切换到DB n，`dbsize`可以查看当前数据库的大小，与key数量相关。

```bash
127.0.0.1:6379> config get databases # 命令行查看数据库数量databases
1) "databases"
2) "16"

127.0.0.1:6379> select 8 # 切换数据库 DB 8
OK
127.0.0.1:6379[8]> dbsize # 查看数据库大小
(integer) 0

# 不同数据库之间 数据是不能互通的，并且dbsize 是根据库中key的个数。
127.0.0.1:6379> set name sakura 
OK
127.0.0.1:6379> SELECT 8
OK
127.0.0.1:6379[8]> get name # db8中并不能获取db0中的键值对。
(nil)
127.0.0.1:6379[8]> DBSIZE
(integer) 0
127.0.0.1:6379[8]> SELECT 0
OK
127.0.0.1:6379> keys *
1) "counter:__rand_int__"
2) "mylist"
3) "name"
4) "key:__rand_int__"
5) "myset:__rand_int__"
127.0.0.1:6379> DBSIZE # size和key个数相关
(integer) 5
```
`keys` ：查看当前数据库中所有的key。

`flushdb`：清空当前数据库中的键值对。

`flushall`：清空所有数据库的键值对。

>**Redis是单线程的，Redis是基于内存操作的。**

所以Redis的性能瓶颈不是CPU,而是机器内存和网络带宽。

那么为什么Redis的速度如此快呢，性能这么高呢？QPS达到10W+

> **Redis为什么单线程还这么快？**

- 误区1：高性能的服务器一定是多线程的？
- 误区2：多线程（CPU上下文会切换！）一定比单线程效率高！

核心：Redis是将所有的数据放在内存中的，所以说使用单线程去操作效率就是最高的，多线程（CPU上下文会切换：耗时的操作！），对于内存系统来说，如果没有上下文切换效率就是最高的，多次读写都是在一个CPU上的，在内存存储数据情况下，单线程就是最佳的方案。

# 五大基本数据类型
Redis可以用作**数据库、缓存、和 消息中间件** 它支持[字符串string](https://www.redis.net.cn/tutorial/3508.html)、[哈希表hashes](https://www.redis.net.cn/tutorial/3509.html)、[列表lists](https://www.redis.net.cn/tutorial/3510.html)、[集合sets](https://www.redis.net.cn/tutorial/3511.html)、[有序集合sorted sets](https://www.redis.net.cn/tutorial/3512.html)，[位图bitmaps](https://www.redis.net.cn/tutorial/3508.html)，[hyperloglogs](https://www.redis.net.cn/tutorial/3513.html)等数据类型。内置复制、[Lua脚本](https://www.redis.net.cn/tutorial/3516.html)、LRU收回、[事务](https://www.redis.net.cn/tutorial/3515.html)以及不同级别磁盘持久化功能，同时通过Redis Sentinel提供高可用，通过Redis Cluster提供自动[分区](https://www.redis.net.cn/tutorial/3524.html)。

## String
### 常用操作		

● SET  key  value 

	存入字符串键值对

● MSET  key  value [key value ...] 	

	批量存储字符串键值对

● SETNX  key  value 		

	只在键 key 不存在的情况下,将键 key 的值设置为 value。
	若键 key 已经存在,则 SETNX 命令不做任何动作。
	SETNX 是『SET if Not exist』的简写。

●APPEND key value

	追加字符串

● GET  key 			

	获取一个字符串键值

● MGET  key  [key ...]	 	

	批量获取字符串键值

● DEL  key  [key ...] 		

	删除一个键

● EXPIRE  key  seconds 	

	设置一个键的过期时间(秒)

### 原子操作 

**INCR**  **key** 	

	将key中储存的数字值加1

**DECR**  **key** 			

	将key中储存的数字值减1

**INCRBY**  **key  increment** 		

	将key所储存的值加上increment

**DECRBY**  **key  decrement** 	

	将key所储存的值减去decrement

### 单值缓存

 **经典使用场景，把常用信息，字符串，图片或者视频等信息放到redis中，redis作为缓存层，mysql做持久化层，降低mysql的读写压力。**

	SET  key  value
	GET  key 

### 对象缓存

	SET  user:1  value(json格式数据)
	MSET  user:1:name   zhuge   user:1:balance  1888
	MGET  user:1:name   user:1:balance

### 分布式锁

**SETNX  product:10001  true**  

	在返回1的时候,代表没人用这个key,获取锁成功

**比如多线程都对redis使用这一条命令，那么只有第一条才能获取1，我们认为他拿到了锁**

	SETNX  product:10001  true  
	很明显第二条进程返回应该是0
	。。。执行业务操作
	DEL  product:10001
	那么我们执行完第一个线程的业务操作,我们就要释放锁,删除就可
	SET product:10001 true  ex  10  nx 
	防止死锁

### 计数器

	INCR article:readcount:{文章id} 
	GET article:readcount:{文章id}
	redis是单线程模型，一个命令执行完才会执行下一个,同时数据可以一步落地到其他的数据源

![image-20220318161207143](E:\Typora\Redis\image-20220318161207143.png)

### Web集群session共享

spring session + redis实现session共享

### 分布式系统全局序列号	

	INCRBY  orderId  1000	//redis批量生成序列号提升性能

## List

双向链表+数组的结合体

	每个元素也有正负索引,值可以重复
	key中有指向head和tail的属性

![image-20220318164551078](E:\Typora\Redis\image-20220318164551078.png)

### 常用操作

| 命令                            | 描述                                   |
| ------------------------------- | -------------------------------------- |
| RPUSH key value1[value2]        | 从右边向列表中PUSH值                   |
| LPUSH key value1[value2]        | 从左边向列表中PUSH值                   |
| LRANGE key start end            | 获取list 起止元素（索引从左往右 递增） |
| LPUSHX key value                | 向已存在的列名中push值                 |
| RPUSHX key value                | 向已存在的列名中push值                 |
| LINSERT key BEFORE pivot value  | 在指定列表元素的前插入value            |
| LINSERT key   AFTER pivot value | 在指定列表元素的后插入value            |
| LLEN key                        | 查看列表长度                           |
| LINDEX key index                | 通过索引获取列表元素                   |
| LSET key index value            | 通过索引为元素设值                     |
| LPOP key                        | 从最左边移除值 并返回                  |
| RPOP key                        | 从最右边移除值 并返回                  |

## Set
集合

![image-20220318172142657](E:\Typora\Redis\image-20220318172142657.png)
### 常用操作

| 命令                                        | 描述                                                   |
| ------------------------------------------- | ------------------------------------------------------ |
| **SADD  key  member  [member ...]**         | 往集合key中存入元素，元素存在则忽略，若key不存在则新建 |
| **SREM  key  member  [member ...]**         | 从集合key中删除元素                                    |
| **SMEMBERS  key**                           | 获取集合key中所有元素                                  |
| **SCARD  key**                              | 获取集合key的元素个数                                  |
| **SISMEMBER  key  member**                  | 判断member元素是否存在于集合key中                      |
| **SRANDMEMBER  key  [count]**               | 从集合key中选出count个元素，元素不从key中删除          |
| **SPOP  key  [count]**                      | 从集合key中选出count个元素，元素从key中删除            |
|                                             |                                                        |
| **SINTER  key  [key ...]**                  | 交集运算                                               |
| **SINTERSTORE  destination  key  [key ..]** | 将交集结果存入新集合destination中                      |
| **SUNION  key  [key ..]**                   | 并集运算                                               |
| **SUNIONSTORE  Destin  key  [key ]**        | 将并集结果存入新集合                                   |
| **SDIFF key [key2 ...]**                    | 差集运算                                               |
| **SDIFFSTORE  destination  key  [key ...]** | 将差集结果存入新集合destination中                      |


### 抽奖
此时做一个抽奖程序，我们来思考一下抽奖程序的过程



1. 用户点击参与抽奖按钮

2. 1. SADD key user:{1001}
   2. SADD key user:{1002}
   3. SADD key user:{1003}

3. redis中要存放该用户参与信息

4. 1. SMEMBERS key     //获取所有用户

5. 抽取获奖用户

6. 1. SRANDMEMBER key 1   //从key中抽取出1个用户
   2. SPOP key 1  //比如抽三等奖后，这个人不能再抽了,就把它抽出来后删了



### 点赞

- **SADD  like:{消息ID}  {用户ID}  点赞**
- 因为对于点赞来说，我们要求点赞是唯一性的，不能重复点赞
- SREM  like:{消息ID}  {用户ID}  取消点赞
- SISMEMBER  like:{消息ID}  {用户ID} 检查用户是否点过赞
- SMEMBERS like:{消息ID}  获取点赞用户列表
- SCARD like:{消息ID} 获取点赞数





### 集合操作

![image-20220318181803386](E:\Typora\Redis\image-20220318181803386.png)

- SUNION set1 set2 set3 -> { a,b,c,d,e }：并集
- SDIFF set1 set2 set3 -> { a } ：差集



### 共同关注

- peng 关注列表 ->> {jack,tom,even}
- jack 关注列表 ->> {peng,tom,ruke}
- tom 关注列表 ->> {peng，jack，ruke，even}
- **peng和jack共同关注的人**

- - SINTER peng jack  -- > {tom}

- **peng关注的人也关注了jack**

- - SISMEMBER tom jack
  - SISMEMBER even jack

- **peng可能认识的人**

- - SDIFF jack tom -> {ruke}



### 电商商品筛选

- SADD  brand:huawei  P40
- SADD  brand:xiaomi  mi-10
- SADD  brand:iPhone iphone12
- SADD os:android  P40  mi-10
- SADD cpu:brand:intel  P40  mi-10
- SADD ram:8G  P40  mi-10  iphone12



在我们系统录入数据的时候就已经提前在redis中添加集合。然后用户此时是需要安卓操作系统，ram要8g

那么我们只需要 **SINTER  os:android  cpu:brand:intel  ram:8G**  --》{P40，mi-10} 就能筛选出我们符合心意的产品

## Hash
一个Map集合，key-map string 类型的 field（字段） 和 value（值） 的映射表

| 命令                                       | 描述                             | 举例                        |
| ------------------------------------------ | -------------------------------- | --------------------------- |
| HSET  key  field  value                    | 存储一个哈希表key的键值          | hset user  username1  jiang |
| HSETNX  key  field  value                  | 存储一个不存在的哈希表key的键值  | 与setnx规则相同             |
| HMSET  key  field  value [field value ...] | 在一个哈希表key中存储多个键值对  |                             |
| HGET  key  field                           | 获取哈希表key对应的field键值     |                             |
| HMGET  key  field  [field ...]             | 批量获取哈希表key中多个field键值 |                             |
| HDEL  key  field  [field ...]              | 删除哈希表key中的field键值       |                             |
| HLEN  key                                  | 返回哈希表key中field的数量       |                             |
| HGETALL key                                | 返回哈希表key中所有的键值        |                             |

### 对象存储

	通过HMSet来设置，能直观，相比string更节省空间的维护缓存信息，如用户信息，视频信息
	因为Redis是单线程模型,所以会有一个严重的问题，就是redis的大key问题，就是比如说我们这个user下有一百万条username10000000，假设我通过hgetall user，那么会把这一百万条数据都读出来，这就是大key问题

###  电商购物车

用户id为key  商品id为field  商品数量为value

![image-20220318182711934](E:\Typora\Redis\image-20220318182711934.png)

- 添加商品 hset cart:1001 10088 1
- 增加数量 hincrby cart:1001 10088 1
- 增加数量 hincrby cart:1001 10088 1
- 删除商品 hdel cart:1001 10088
- 获取购物车所有商品 hgetall cart:1001



### 优点

1. 同类数据归类整合储存，方便数据管理
2. 相比string操作消耗内存与cpu更小
3. 相比string储存更节省空间
3.  **Hash更适合于对象的存储，Sring更加适合字符串存储！**Hash适合经常变更的数据，尤其是用户信息之类的，经常变动的信息！



### 缺点

1. 过期功能不能使用在field上，只能用在key上
2. Redis集群架构下不适合大规模使用



## Zset

sort_set，集合的一种特殊方式

![image-20220318181437576](E:\Typora\Redis\image-20220318181437576.png)

![image-20220318181454480](E:\Typora\Redis\image-20220318181454480.png)

### 基本操作

| 命令                                    | 描述                                           |
| --------------------------------------- | ---------------------------------------------- |
| ZADD key score member [[score member]…] | 往有序集合key中加入带分值元素                  |
| ZREM key member [member …]              | 从有序集合key中删除元素                        |
| ZSCORE key member                       | 返回有序集合key中元素member的分值              |
| ZINCRBY key increment member            | 为有序集合key中元素member的分值加上increment   |
| ZCARD key                               | 返回有序集合key中元素个数                      |
| ZRANGE key start stop [WITHSCORES]      | 正序获取有序集合key从start下标到stop下标的元素 |
| ZREVRANGE key start stop [WITHSCORES]   | 倒序获取有序集合key从start下标到stop下标的元素 |
| ZINTERSTORE destkey numkeys key [key …] | 交集计算                                       |



### 热搜排行榜

![img](https://cdn.nlark.com/yuque/0/2022/png/12995622/1646817731952-28b4de1b-8b08-4eb5-b9b5-3e4b2a82c4d6.png)

#### 面向用户的Zset指令

点击新闻

	ZINCRBY  hotNews:20190819  1  守护香港	

展示当日排行前十

	ZREVRANGE  hotNews:20190819  0  9  WITHSCORES

七日搜索榜单计算

	ZUNIONSTORE  hotNews:20190813-20190819  7 hotNews:20190813  hotNews:20190814... hotNews:20190819
	hotNews:20190813-20190819 ：七天内都上榜的新闻

展示七日排行前十

	ZREVRANGE hotNews:20190813-20190819  0  9  WITHSCORES



# 三种特殊数据类型

## Mysql存储地理

	我们考虑问题的时候,有两个方面,存的进,取得出
	然后当我们要找车时,在数据库中查找距离我们(坐标x0,y0)附近r公里范围内部的车辆
```mysql
select taxi from position where x0-r <x<x0 +r and y0-r < y < y0+r
```
### 缺点
	查询性能问题，如果并发高，数据量大这种查询是要搞垮数据库的
	这个查询的是一个矩形访问，而不是以我为中心公里为半径的圆形访问。
	精准度的问题，我们知道地球不是平面坐标系，而是一个圆球，这种矩形计算在长距离计算时会有很大误差

## Geo
geospatial	地理位置，朋友的定位，打车距离计算

经纬查询网站：[巨野县经纬度-巨野县经度纬度-山东省菏泽市巨野县经纬度查询工具 (jsons.cn)](http://www.jsons.cn/lngcodeinfo/7A083F96B2A8D58E)

### 原理
	将三维的地球转为二维的坐标
	将二维的坐标转为一纬的点块
	最后将一维的点块转为二进制再通过base32编码

### **GEORADIUS的参数**

> 通过`georadius`就可以完成 **附近的人**功能
>
> withcoord:带上坐标
>
> withdist:带上距离，单位与半径单位相同
>
> COUNT n : 只显示前n个(按距离递增排序)

### 有效经纬度
	有效的经度从-180度到180度。
	有效的纬度从-85.05112878度到85.05112878度。

### 单位
- **m** 表示单位为米
- **km** 表示单位为千米
- **mi** 表示单位为英里
- **ft** 表示单位为英尺

### 基本操作 

我们要确定位置（x0,y0），这是经纬度来进行定位的。

| 命令                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `geoadd key longitud(经度) latitude(纬度) member [..]`       | 将具体经纬度的坐标存入一个有序集合                           |
| `geopos key member [member..]`                               | 获取集合中的一个/多个成员坐标                                |
| `geodist key member1 member2 [unit]`                         | 返回两个给定位置之间的距离。默认以米作为单位。               |
| `georadius key longitude latitude radius m|km|mi|ft [WITHCOORD][WITHDIST] [WITHHASH] [COUNT count]` | 以给定的经纬度为中心， 返回集合包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。 |
| `GEORADIUSBYMEMBER key member radius...`                     | 功能与GEORADIUS相同，只是中心位置是使用结合中已有的成员作为中心点。 |
| `geohash key member1 [member2..]`                            | 返回一个或多个位置元素的Geohash表示。使用Geohash位置52点整数编码。 |

### 美团案例

	将附近商场超市饭店 geoadd到redis中
	此时获取我的坐标系
	然后georadius可以获取我附近的店铺


## Hyperloglog
	基数统计,优点是在输入元素的数量或者体积非常非常大时,计算基数所需的空间总是固定的、并且是很小的。
>
> 花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基数。
>
> 因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。
>
> 其底层使用string数据类型

### 基数

> 数据集中不重复的元素的个数

### 应用场景

网页的访问量（UV）：一个用户多次访问，也只能算作一个人。

> 传统实现，存储用户的id,然后每次进行比较。当用户变多之后这种方式及其浪费空间，而我们的目的只是**计数**，Hyperloglog就能帮助我们利用最小的空间完成。

| 命令                                      | 描述                                      |
| ----------------------------------------- | ----------------------------------------- |
| `PFADD key element1 [elememt2..]`         | 添加指定元素到 HyperLogLog 中             |
| `PFCOUNT key [key]`                       | 返回给定 HyperLogLog 的基数估算值。       |
| `PFMERGE destkey sourcekey [sourcekey..]` | 将多个 HyperLogLog 合并为一个 HyperLogLog |

```bash
----------PFADD--PFCOUNT---------------------
127.0.0.1:6379> PFADD myelemx a b c d e f g h i j k # 添加元素
(integer) 1
127.0.0.1:6379> type myelemx # hyperloglog底层使用String
string
127.0.0.1:6379> PFCOUNT myelemx # 估算myelemx的基数
(integer) 11
127.0.0.1:6379> PFADD myelemy i j k z m c b v p q s
(integer) 1
127.0.0.1:6379> PFCOUNT myelemy
(integer) 11

----------------PFMERGE-----------------------
127.0.0.1:6379> PFMERGE myelemz myelemx myelemy # 合并myelemx和myelemy 成为myelemz
OK
127.0.0.1:6379> PFCOUNT myelemz # 估算基数
(integer) 17
```

如果允许容错，那么一定可以使用Hyperloglog !

如果不允许容错，就使用set或者自己的数据类型即可 ！


## Bitmap
> 使用位存储，信息状态只有 0 和 1
>
> 位图是一串连续的2进制数字（0或1），每一位所在的位置为偏移(offset)，在bitmap上可执行AND,OR,XOR,NOT以及其它位操作。

### 应用场景

	统计用户信息、活跃、不活跃！登录、未登录！打开，未打卡！

### 基本操作
| 命令                                  | 描述                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| `setbit key offset value`             | 为指定key的offset位设置值                                    |
| `getbit key offset`                   | 获取offset位的值                                             |
| `bitcount key [start end]`            | 统计字符串被设置为1的bit数，也可以指定统计范围按字节         |
| `bitop operration destkey key[key..]` | 对一个或多个保存二进制位的字符串 key 进行位元操作，并将结果保存到 destkey 上。 |
| `BITPOS key bit [start] [end]`        | 返回字符串里面第一个被设置为1或者0的bit位。start和end只能按字节,不能按位 |

```bash
------------setbit--getbit--------------
127.0.0.1:6379> setbit sign 0 1 # 设置sign的第0位为 1 
(integer) 0
127.0.0.1:6379> setbit sign 2 1 # 设置sign的第2位为 1  不设置默认 是0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 5 1
(integer) 0
127.0.0.1:6379> type sign
string

127.0.0.1:6379> getbit sign 2 # 获取第2位的数值
(integer) 1
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 4 # 未设置默认是0
(integer) 0

-----------bitcount----------------------------
127.0.0.1:6379> BITCOUNT sign # 统计sign中为1的位数
(integer) 4
```

**bitmaps的底层**

![image-20220319130519350](E:\Typora\Redis\image-20220319130519350.png)




# 配置文件
# 持久化
## RDB
Redis Database，在指定的时间间隔内将内存中的数据集写入磁盘，俗称快照

![image-20220320121105131](E:\Typora\Redis\image-20220320121105131.png)

### 过程
	Redis会单独分支（fork）一个子进程，它会先将数据写入到一个临时文件中，待持久化结束后，在用临时文件替换上次被持久化好的文件。
### 优点
	整个过程中，主进程是不进行任何IO操作，这确保了极高的性能。
	如果需要进行大规模数据的恢复,并且对于数据恢复的完整性不是非常敏感,那么RDB方式要比AOF方式更加的高效。

### 缺点
	最后一次持久化的数据可能丢失。

### 触发机制
	save的规则满足的情况下会自动触发rdb原则
	执行flushall命令，也会触发我们的rdb原则
	退出redis，也会自动产生rdb文件

### save

![image-20220320121625970](E:\Typora\Redis\image-20220320121625970.png)

```bash
#持久化规则，持久化到文件 .rdb .aof
#900秒内 至少一个key进行了修改，就进行持久化
save 900 10
#300秒内 至少10个key进行了...
save 60 10000
```

#### bgsave

`bgsave` 是异步进行，进行持久化的时候，`redis` 还可以将继续响应客户端请求 ；

![image-20220320122804979](E:\Typora\Redis\image-20220320122804979.png)

**bgsave和save对比**

| 命令   | save               | bgsave                             |
| ------ | ------------------ | ---------------------------------- |
| IO类型 | 同步               | 异步                               |
| 阻塞？ | 是                 | 是（阻塞发生在fock()，通常非常快） |
| 复杂度 | O(n)               | O(n)                               |
| 优点   | 不会消耗额外的内存 | 不阻塞客户端命令                   |
| 缺点   | 阻塞客户端命令     | 需要fock子进程，消耗内存           |

### 优缺点

**优点：**

1. 适合大规模的数据恢复
2. 对数据的完整性要求不高

**缺点：**

1. 需要一定的时间间隔进行操作，如果redis意外宕机了，这个最后一次修改的数据就没有了。
2. fork进程的时候，会占用一定的内容空间。




## AOF

 Append Only File,将我们所有的命令都记录下来

将我们所有的命令都记录下来，history，恢复的时候就把这个文件全部再执行一遍

> 以日志的形式来记录每个写的操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

### AOF相对于RDB

 快照功能（RDB）并不是非常耐久（durable）： 如果 Redis 因为某些原因而造成故障停机， 那么服务器将丢失最近写入、以及未保存到快照中的那些数据。 从 1.1 版本开始， Redis 增加了一种完全耐久的持久化方式： AOF 持久化。

如果要使用AOF，需要修改配置文件：

![image-20220320125055480](E:\Typora\Redis\image-20220320125055480.png)

`appendonly no yes`则表示启用AOF

默认是不开启的，我们需要手动配置，然后重启redis，就可以生效了！

如果这个aof文件有错位，这时候redis是启动不起来的，我需要修改这个aof文件

redis给我们提供了一个工具`redis-check-aof --fix`

> 优点和缺点

```bash
appendonly yes  # 默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分的情况下，rdb完全够用
appendfilename "appendonly.aof"

# appendfsync always # 每次修改都会sync 消耗性能
appendfsync everysec # 每秒执行一次 sync 可能会丢失这一秒的数据
# appendfsync no # 不执行 sync ,这时候操作系统自己同步数据，速度最快
```

**优点**

1. 每一次修改都会同步，文件的完整性会更加好
2. 没秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率最高

**缺点**

1. 相对于数据文件来说，aof远远大于rdb，修复速度比rdb慢！
2. Aof运行效率也要比rdb慢，所以我们redis默认的配置就是rdb持久化

### RDB 和 AOF 对比

| RDB        | AOF    |              |
| ---------- | ------ | ------------ |
| 启动优先级 | 低     | 高           |
| 体积       | 小     | 大           |
| 恢复速度   | 快     | 慢           |
| 数据安全性 | 丢数据 | 根据策略决定 |

### 如何选择使用哪种持久化方式？

一般来说， 如果想达到足以媲美 PostgreSQL 的数据安全性， 你应该同时使用两种持久化功能。

如果你非常关心你的数据， 但仍然可以承受数分钟以内的数据丢失， 那么你可以只使用 RDB 持久化。

有很多用户都只使用 AOF 持久化， 但并不推荐这种方式： 因为定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快。







# 事务

## 特性
事务中每条命令都会被序列化，执行过程中按顺序执行，不允许其他命令进行干扰。

	一次性
	顺序性
	排他性

**Redis事务没有隔离级别的概念,redis事务不能保证原子性**

## 操作过程

	开启事务（`multi`）
	命令入队
	执行事务（`exec`）

所以事务中的命令在加入时都没有被执行，直到提交时才会开始执行(Exec)一次性完成。

**放弃事务(`discurd`)**

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> DISCARD # 放弃事务
OK
127.0.0.1:6379> EXEC 
(error) ERR EXEC without MULTI # 当前未开启事务
127.0.0.1:6379> get k1 # 被放弃事务中命令并未执行
(nil)
```

## 事务错误
> 代码语法错误（编译时异常）所有的命令都不执行

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> error k1 # 这是一条语法错误命令
(error) ERR unknown command `error`, with args beginning with: `k1`, # 会报错但是不影响后续命令入队 
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> EXEC
(error) EXECABORT Transaction discarded because of previous errors. # 执行报错
127.0.0.1:6379> get k1 
(nil) # 其他命令并没有被执行
```

> 代码逻辑错误 (运行时异常) **其他命令可以正常执行 ** >>> 所以不保证事务原子性

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> INCR k1 # 这条命令逻辑错误（对字符串进行增量）
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> exec
1) OK
2) OK
3) (error) ERR value is not an integer or out of range # 运行时报错
4) "v2" # 其他命令正常执行
```
### 总结
	有一条命令报错了,但是后面的指令依旧正常执行成功了。
	所以说Redis单条指令保证原子性，但是Redis事务不能保证原子性。

## 监控
### 悲观锁
	很悲观，认为什么时候都会出现问题，无论做什么都会加锁

### 乐观锁
	很乐观，认为什么时候都不会出现问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据
	获取version、更新的时候比较version
	使用`watch key`监控指定数据，相当于乐观锁加锁。

> 正常执行

```bash
127.0.0.1:6379> set money 100 # 设置余额:100
OK
127.0.0.1:6379> set use 0 # 支出使用:0
OK
127.0.0.1:6379> watch money # 监视money (上锁)
OK
127.0.0.1:6379> multi #执行事务,事务正常结束,数据期间没有发生变动
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY use 20
QUEUED
127.0.0.1:6379> exec # 监视值没有被中途修改，事务正常执行
1) (integer) 80
2) (integer) 20
```

> 测试多线程修改值，使用watch可以当做redis的乐观锁操作（相当于getversion）

我们启动另外一个客户端模拟插队线程。

线程1：

```bash
127.0.0.1:6379> watch money # money上锁
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 20
QUEUED
127.0.0.1:6379> INCRBY use 20
QUEUED
127.0.0.1:6379> 	# 此时事务并没有执行
```

模拟线程插队，线程2：

```bash
127.0.0.1:6379> INCRBY money 500 # 修改了线程一中监视的money
(integer) 600
12
```

回到线程1，执行事务：

```bash
127.0.0.1:6379> EXEC # 执行之前，另一个线程修改了我们的值，这个时候就会导致事务执行失败
(nil) # 没有结果，说明事务执行失败

127.0.0.1:6379> get money # 线程2 修改生效
"600"
127.0.0.1:6379> get use # 线程1事务执行失败，数值没有被修改
"0"
```

> 解锁获取最新值，然后再加锁进行事务。
>
> `unwatch`进行解锁。

注意：每次提交执行exec后都会自动释放锁，不管是否成功


## 订阅发布
## 主从复制
## 哨兵模式

**主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费事费力，还会造成一段时间内服务不可用。**这不是一种推荐的方式，更多时候，我们优先考虑**哨兵模式**。

单机单个哨兵

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-2ENYVAPp-1597890996527)(狂神说 Redis.assets/image-20200818233231154.png)]

哨兵的作用：

- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。
- 当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。

多哨兵模式

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-Ga1RyfVc-1597890996528)(狂神说 Redis.assets/image-20200818233316478.png)]

哨兵的核心配置

```
sentinel monitor mymaster 127.0.0.1 6379 1
```

- 数字1表示 ：当一个哨兵主观认为主机断开，就可以客观认为主机故障，然后开始选举新的主机。

> 测试

```
redis-sentinel xxx/sentinel.conf
```

成功启动哨兵模式

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513215752444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MzIyNw==,size_16,color_FFFFFF,t_70)

此时哨兵监视着我们的主机6379，当我们断开主机后：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513215806972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MzIyNw==,size_16,color_FFFFFF,t_70)

> 哨兵模式优缺点

**优点：**

1. 哨兵集群，基于主从复制模式，所有主从复制的优点，它都有
2. 主从可以切换，故障可以转移，系统的可用性更好
3. 哨兵模式是主从模式的升级，手动到自动，更加健壮

**缺点：**

1. Redis不好在线扩容，集群容量一旦达到上限，在线扩容就十分麻烦
2. 实现哨兵模式的配置其实是很麻烦的，里面有很多配置项

> 哨兵模式的全部配置

完整的哨兵模式配置文件 sentinel.conf

```bash
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 1
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，
这个数字越小，完成failover所需的时间就越长，
但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。
可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 
 
 
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，
#这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，
#一个是事件的类型，
#一个是事件的描述。
#如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
```

## 十六、缓存穿透与雪崩

### 缓存穿透（查不到）

> 概念

在默认情况下，用户请求数据时，会先在缓存(Redis)中查找，若没找到即缓存未命中，再在数据库中进行查找，数量少可能问题不大，可是一旦大量的请求数据（例如秒杀场景）缓存都没有命中的话，就会全部转移到数据库上，造成数据库极大的压力，就有可能导致数据库崩溃。网络安全中也有人恶意使用这种手段进行攻击被称为洪水攻击。

> 解决方案

**布隆过滤器**

对所有可能查询的参数以Hash的形式存储，以便快速确定是否存在这个值，在控制层先进行拦截校验，校验不通过直接打回，减轻了存储系统的压力。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513215824722.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MzIyNw==,size_16,color_FFFFFF,t_70)

**缓存空对象**

一次请求若在缓存和数据库中都没找到，就在缓存中方一个空对象用于处理后续这个请求。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513215836317.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MzIyNw==,size_16,color_FFFFFF,t_70)

 这样做有一个缺陷：存储空对象也需要空间，大量的空对象会耗费一定的空间，存储效率并不高。解决这个缺陷的方式就是设置较短过期时间

即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于需要保持一致性的业务会有影响。

### 缓存击穿（量太大，缓存过期）

> 概念

 相较于缓存穿透，缓存击穿的目的性更强，一个存在的key，在缓存过期的一刻，同时有大量的请求，这些请求都会击穿到DB，造成瞬时DB请求量大、压力骤增。这就是缓存被击穿，只是针对其中某个key的缓存不可用而导致击穿，但是其他的key依然可以使用缓存响应。

 比如热搜排行上，一个热点新闻被同时大量访问就可能导致缓存击穿。

> 解决方案

1. **设置热点数据永不过期**

   这样就不会出现热点数据过期的情况，但是当Redis内存空间满的时候也会清理部分数据，而且此种方案会占用空间，一旦热点数据多了起来，就会占用部分空间。

2. **加互斥锁(分布式锁)**

   在访问key之前，采用SETNX（set if not exists）来设置另一个短期key来锁住当前key的访问，访问结束再删除该短期key。保证同时刻只有一个线程访问。这样对锁的要求就十分高。

### 缓存雪崩

> 概念

大量的key设置了相同的过期时间，导致在缓存在同一时刻全部失效，造成瞬时DB请求量大、压力骤增，引起雪崩。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200513215850428.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MzIyNw==,size_16,color_FFFFFF,t_70)

> 解决方案

- redis高可用

  这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是搭建的集群

- 限流降级

  这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个key只允许一个线程查询数据和写缓存，其他线程等待。

- 数据预热

  数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。

## Jedis
使用Java来操作Redis，Jedis是Redis官方推荐使用的Java连接redis的客户端。

### 导入依赖
```xml
   <!--导入jredis的包-->
   <dependency>
       <groupId>redis.clients</groupId>
       <artifactId>jedis</artifactId>
       <version>3.2.0</version>
   </dependency>
   <!--fastjson-->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>fastjson</artifactId>
       <version>1.2.70</version>
   </dependency>
```
### 编码测试

   - 连接数据库

     1. 修改redis的配置文件

        ```bash
        vim /usr/local/bin/myconfig/redis.conf
        1
        ```

        1. 将只绑定本地注释

           [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-4IRUFJ95-1597890996520)(狂神说 Redis.assets/image-20200813161921480.png)]

        2. 保护模式改为 no

           [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-oKjIVapw-1597890996521)(狂神说 Redis.assets/image-20200813161939847.png)]

        3. 允许后台运行

           [外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-c2IMvpZL-1597890996522)(狂神说 Redis.assets/image-20200813161954567.png)]

3. 开放端口6379

   ```bash
   firewall-cmd --zone=public --add-port=6379/tcp --permanet
   1
   ```

   重启防火墙服务

   ```bash
   systemctl restart firewalld.service
   1
   ```

   1. 阿里云服务器控制台配置安全组

   2. 重启redis-server

      ```bash
      [root@AlibabaECS bin]# redis-server myconfig/redis.conf 
      1
      ```



- 操作命令

  **TestPing.java**

  ```java
  public class TestPing {
      public static void main(String[] args) {
          Jedis jedis = new Jedis("192.168.xx.xxx", 6379);
          String response = jedis.ping();
          System.out.println(response); // PONG
      }
  }
  ```

- 断开连接

1. **事务**

```java
   public class TestTX {
       public static void main(String[] args) {
           Jedis jedis = new Jedis("39.99.xxx.xx", 6379);
   
           JSONObject jsonObject = new JSONObject();
           jsonObject.put("hello", "world");
           jsonObject.put("name", "kuangshen");
           // 开启事务
           Transaction multi = jedis.multi();
           String result = jsonObject.toJSONString();
           // jedis.watch(result)
           try {
               multi.set("user1", result);
               multi.set("user2", result);
               // 执行事务
               multi.exec();
           }catch (Exception e){
               // 放弃事务
               multi.discard();
           } finally {
               // 关闭连接
               System.out.println(jedis.get("user1"));
               System.out.println(jedis.get("user2"));
               jedis.close();
           }
       }
   }
```



## SpringBoot
## 实践分析

