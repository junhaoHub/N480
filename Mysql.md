

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
	在面对数据量庞大的数据时，无法判断其层数，会造成大量的IO

## Hash

![image-20220310113257739](E:\Typora\Mysql\image-20220310113257739.png)

	对索引的key进行一次hash计算就可以定位出数据存储的位置
	很多时候Hash索引要比B+树索引更高效
	仅能满足 "=","IN",不支持范围查询
### hash冲突问题


## B树

![image-20220310113034625](E:\Typora\Mysql\image-20220310113034625.png)

	叶节点具有相同的深度，叶节点的指针为空
	所有索引元素不重复
	节点中的数据索引从左到右递增排列

## B+树

![image-20220310113105322](E:\Typora\Mysql\image-20220310113105322.png)

	根节点不存储data，只存储索引（冗余），可以增加更多的索引
	叶子节点包含所有的索引字段
	叶子节点用指针连接，提高区间访问的性能

## 面试题：如何用B+树快速查找

## 聚集索引
## 聚簇索引
## 稀疏索引

## 面试题：为什么推荐用自增主键做索引
## 联合索引
## 最左前缀优化原则




# Explain
# 一条SQL是如何执行的
# 索引优化
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









