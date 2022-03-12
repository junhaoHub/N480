

## 使用root创建用户
```mysql
create user junhao identified by '123456';
```

## 授予权限
```mysql
grant select,insert,delete,update on [TABLENAME].* to junhao@localhost identified by '123456';
```
# 索引
利用数据结构，将数据排好序，以帮助Mysql高效获取

	从存储结构划分 —— B Tree索引、Hash索引、FULLTEXT全文索引、R Tree索引
	从应用层次划分 —— 普通索引、唯一索引、主键索引、复合索引
	从索引键值类型划分 —— 主键索引、辅助索引（二级索引）
	从数据存储和索引键值逻辑关系划分 —— 聚集索引（聚簇索引）、非聚集索引（非聚簇索引）


## 二叉树

![image-20220310094701223](E:\Typora\Mysql\image-20220310094701223.png)

## 红黑树
自平衡二叉查找树，是在计算机科学中用到的一种数据结构，典型的用途是实现关联数组

	优点-红黑树能够以O(log2(N))的时间复杂度进行搜索、插入、删除操作。此外,任何不平衡都会在3次旋转之内解决。
	缺点-在面对数据量庞大的数据时,无法判断其层数,会造成大量的IO

## Hash

![image-20220310113257739](E:\Typora\Mysql\image-20220310113257739.png)

	对索引的key进行一次hash计算就可以定位出数据存储的位置
	很多时候Hash索引要比B+树索引更高效
	仅能满足 "=","IN",不支持范围查询
### hash冲突问题


## B树

![image-20220310113034625](E:\Typora\Mysql\image-20220310113034625.png)

	叶节点具有相同的深度,叶节点的指针为空
	所有索引元素不重复
	节点中的数据索引从左到右递增排列

## B+树

![image-20220310113105322](E:\Typora\Mysql\image-20220310113105322.png)

	根节点不存储data,只存储索引（冗余）,可以增加更多的索引
	叶子节点包含所有的索引字段
	叶子节点用指针连接,提高区间访问的性能

## 面试题：如何用B+树快速查找

## Myisam结构

![image-20220310180557883](E:\Typora\Mysql\image-20220310180557883.png)

### .frm
	数据库存的表的结构

### .MYD
	数据库表中存的数据

### .MYI
	数据库表的索引文件

## InnoDB文件结构

![image-20220310191345114](E:\Typora\Mysql\image-20220310191345114.png)

### .frm
	数据库存的表的结构
### .ibd
	数据库的表数据文件，是按照B+树组织的一个索引结构文件

![image-20220310193253048](E:\Typora\Mysql\image-20220310193253048.png)

## 聚集索引
	叶子节点包含了完整的数据结构

![image-20220310203704882](E:\Typora\Mysql\image-20220310203704882.png)

## 稀疏索引

	MyISAM索引文件和数据文件是分离的（非聚集）

![image-20220310204158998](E:\Typora\Mysql\image-20220310204158998.png)

## 面试题：为什么推荐用自增主键做索引

## 联合索引

![img](E:\Typora\Mysql\C0548F7E902ED4E16CA1F94EDA32D216.png)

## 最左前缀优化原则
	mysql会一直向右匹配直到遇到范围查询（>、<、between、like）就停止匹配,开始在这个范围进行update、select.比如a=3 and b=4 and c>5 and d=6 如果建立（a、b、c、d）顺序的索引,d是用不到索引的，如果建立(a、b、d、c)的索引则都可以用到,a、b、d的顺序可以任意调整。


	=和in可以乱序，比如a=1 and b=2 and c=3建立（a、b、c）索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

# Explain
可以模拟优化器执行SQL语句，分析你的查询语句或是结构的性能瓶颈

```mysql
explain extended select * from film where id =1;
```

![image-20220311092448307](E:\Typora\Mysql\image-20220311092448307.png)

## id
	有几个select就有几个id,并且id的顺序是按select出现的顺序增长的
	id越大执行优先级就越高,id相同则从上往下执行,id为NULL最后执行

## select_type
对应行是简单还是复杂的查询

	simple_简单查询,查询不包含子查询和union
	primary_复杂查询中最外层的select
	subquery_在select后的子查询（不在from字句中）
	derived_在from字句后的子查询。Mysql会将结果存放在一个临时表中，也称为派生类

## table
	查询的语句所关联的表

## type
这一列表示Mysql决定如何查找表中的行，查找数据行记录的大概范围,依次从优到差分别是

	system>const>eq_ref>ref>range>index>ALL
	index-扫描全索引就能拿到结果,一般是扫描某个二级索引
	ALL-全表扫描,扫描你的聚集索引的所有叶子节点


## key_len
用到的索引长度，当用and关键字时，索引长度会随着条件而叠加
![image-20220312092841677](E:\Typora\Mysql\image-20220312092841677.png)

### 计算规则

	char(n)-n字节长度
	varchar-如果是ntf-8，则长度是3n+2字节，加的2字节用来存储字符串长度,n是varchar自定义的长度
	tinyint-1字节
	smallint-2字节
	int-4字节
	bigint-8字节
	date-3字节
	timestamp-4字节
	datetime-8字节
	如果字段允许为NULL,还需要一个字节记录是否为NULL

## ref

	按主键或唯一键读取,将一个主键放到 where 后面作为条件查询,优化器就能把这次查询优化转化为一个常量(const)

## 面试题：mysql在什么时会选择使用全表索引
	在使用不等于（!=或者<>）的时候
	当范围查询的效率没有全表查询的效率高时
	is null,is not null
	like以通配符开头('$abc...')('%abc...')
	少用or或in，用它查询时，mysql不一定使用索引

## 索引使用总结
假设index(a、b、c)

| Where语句                                        | 索引是否被使用                        |
| ------------------------------------------------ | ------------------------------------- |
| where a= 3                                       | Y，使用到a                            |
| where a=3 and b=5                                | Y，使用到a，b                         |
| where a= 3 and b=5 and c=4                       | Y， 使用到a,  b,  c                   |
| where b=3 或者 where b=3 and c=4  或者 where c=4 | N                                     |
| where a=3 and c=5                                | 使用到a，但是c不可以， b中间断了      |
| where a=3 and b>4 and c=5                        | 使用到a和b，c不能用在范围之后， b断了 |
| where a=3 and b like 'kk%' and c= 4              | Y,  使用到a, b, c                     |
| where a=3 and b like '%kk' and  c=4              | Y,  只用到a                           |
| wherea=3 and b like '%kk%' and c=4               | Y, 只用到a                            |
| wherea=3 and b like 'k%kk%' andc=4               | Y,使用到a,b,c                         |



	like KK%相当于=常量,%KK和%KK%相当于范围




# B+树底层分析



# 一条SQL是如何执行的
# 常见索引优化
```mysql
SHOW VARIABLES LIKE '%query%'
	看慢查询日志
slow_query_log
	默认是off关闭的,用时,要改为on打开
slow_query_log_file
	记录的是慢日志的记录文件
long_query_time
	默认是10S,次执行的sql达到这个时长,会被记录
SHOW STATUS LIKE '%slow. _queries%'
	查看慢查询状态
Slow_queries
	记录的是慢查询数量,当有一条sq|执行一次比较慢时,这个value就是1

SET GLOBAL slow_query.log = ON
	打开慢查询
SET GLOBAL long_query.time = 1
	将默认时间改为1s
```

# 锁
# 事务隔离级别
每个数据库连接都有一个全局变量@@tx_isolation,表示当前的事务隔离级别

## 查看隔离级别
	select @@tx_isolation;
## 设置隔离级别
	set transaction isolation level read commit;
## 设置全局的隔离级别
	set global transaction isolation level read commit;
# MVCC
# BufferPool









