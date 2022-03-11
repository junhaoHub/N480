

# 创建用户
```mysql
create user junhao identified by '123456';
```

## 授予权限
```mysql
#授予通过网络方式登录的junhao,对所有库所有表的权限
grant all privileges on *.* to junhao@'%' identified by '123456';

#给junhao用户使用本地命令行方式，授予某个库下的所有表的增删改查的权限
grant select,insert,delete,update on [TABLENAME].* to junhao@localhost identified by '123456';
```
# 索引
利用数据结构，将数据排好序，以帮助Mysql高效获取

## 二叉树

![image-20220310094701223](E:\Typora\Mysql\image-20220310094701223.png)

## 红黑树
自平衡二叉查找树，是在计算机科学中用到的一种数据结构，典型的用途是实现关联数组
### 优点
	红黑树能够以O(log2(N))的时间复杂度进行搜索、插入、删除操作。此外,任何不平衡都会在3次旋转之内解决。
### 缺点
	在面对数据量庞大的数据时,无法判断其层数,会造成大量的IO

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

## 代码示例
```mysql
explain extended select * from film where id =1;
```

![image-20220311092448307](E:\Typora\Mysql\image-20220311092448307.png)

## extended
	explain 的extended 扩展能够在原本explain的基础上额外的提供一些查询优化的信息,这些信息可以通过MySQL的show warnings命令得到.

## const

	直接按主键或唯一键读取,通常情况下,如果将一个主键放置到 where 后面作为条件查询,mysql 优化器就能把这次查询优化转化为一个常量.

# B+树底层分析



# 一条SQL是如何执行的
# 常见索引优化
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









