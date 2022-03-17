

# 用户

## 创建用户
```mysql
create user junhao identified by '123456';
```

## 授予权限
```mysql
grant select,insert,delete,update on [TABLENAME].* to junhao@localhost identified by '123456';
```
# 索引
利用数据结构，将数据排好序，以帮助Mysql高效获取

	存储结构划分 —— B+ Tree索引、Hash索引、红黑树
	应用层次划分 —— 普通索引、唯一索引、主键索引、复合索引
	索引键值类型划分 —— 主键索引、辅助索引（二级索引）
	数据存储和索引键值逻辑关系划分 —— 聚集索引（聚簇索引）、非聚集索引（非聚簇索引）

## 存储结构
### 二叉树

![image-20220310094701223](E:\Typora\Mysql\image-20220310094701223.png)

### B Tree
	所有索引元素不重复
	节点中的数据索引从左到右递增排列
	叶节点具有相同的深度,叶节点的指针为空
![image-20220310113034625](E:\Typora\Mysql\image-20220310113034625.png)

### B+ Tree
	叶子节点包含所有的索引字段
	叶子节点用指针连接,提高区间访问的性能
	根节点不存储data,只存储索引（冗余）,可以增加更多的索引

![image-20220310113105322](E:\Typora\Mysql\image-20220310113105322.png)

### Hash表
	仅能满足 "=" "IN" 不支持范围查询
	很多时候Hash索引要比B+树索引更高效
	对索引的key进行一次hash计算就可以定位出数据存储的位置

![image-20220310113257739](E:\Typora\Mysql\image-20220310113257739.png)
#### 哈希冲突
当采用hash函数算法  H(K) = K mod M

    H(K) = 217 % 7 = 0
    H(K) = 701 % 7 = 1
    H(K) = 19 % 7 = 2
    H(K) = 30 % 7 = 2
    H(K) = 145 % 7 = 5

![image-20220316101737161](E:\Typora\Mysql\image-20220316101737161.png)

#### 解决方案：线性探测
	19 和 30 就发生冲突了
	使用散列函数H（K）在大小为M的表中插入密钥K时：
	设置 indx = H（K）
	如果表位置indx已经包含密钥，则无需插入它
	否则，如果表位置indx为空，则在其中插入键
	其他碰撞。设置 indx =（indx + 1）mod M.
	如果 indx == H（K），则表已满！就只能做哈希表的扩容了
	因此，线性探测基本上是在发生碰撞时对空槽进行线性搜索。


### R-B Tree
红黑树，又称自平衡二叉查找树，是在计算机科学中用到的一种数据结构，典型是实现关联数组
#### 规则
	根节点必为黑色
	节点分为红色或者黑色
	叶子节点都为黑色，且为null

![image-20220316102956191](E:\Typora\Mysql\image-20220316102956191.png)


	优点：红黑树能够以O(log2(N))的时间复杂度进行搜索、插入、删除操作。此外，任何不平衡都会在3次旋转之内解决。
	缺点：在面对数据量庞大的数据时,无法判断其层数,会造成大量的IO


# 存储引擎
## Myisam
只支持表锁，高并发场景下会出现严重的问题

![image-20220316105355803](E:\Typora\Mysql\image-20220316105355803.png)

	.frm_数据库存的表的结构
	.MYD_数据库表中存的数据
	.MYI_数据库表的索引文件

## InnoDB
按照B+树组织的一个索引结构文件，支持行锁

![image-20220316105811214](E:\Typora\Mysql\image-20220316105811214.png)

	.frm_数据库存的表的结构
	.ibd_数据库的表数据文件



## 聚集索引
叶子节点包含了完整的数据结构，被Innodb所应用
![image-20220310193253048](E:\Typora\Mysql\image-20220310193253048.png)

## 面试题：B+树是如何有效查询的
	以查询25为例
	第一次磁盘I/O:访问根结点[15、56],发现25小于 [15、56] 中的 56,则访问根结点的第一个孩子结点
	第二次磁盘I/O:访问根结点[15、20、49],发现25大于20且小于49,则访问当前结点的第三个孩子结点 [20、30].
	第三次磁盘I/O:访问叶子结点 [20、30],顺序遍历结点内部，找到要查找的元素 25.



## 稀疏索引

索引文件和数据文件是分离的，被MyISAM所应用
![image-20220310204158998](E:\Typora\Mysql\image-20220310204158998.png)

## 面试题：为什么推荐用自增主键做索引

![image-20220316155850478](E:\Typora\Mysql\image-20220316155850478.png)

	如果表使用自增主键,每次插入新的记录,记录就会顺序添加到当前索引节点的后续位置
	这样就会形成一个紧凑的索引结构,近似顺序填充
	由于每次插入时也不需要移动已有数据率很高,也不会增加很多开销在维护索引上


## 联合索引（覆盖索引）

![image-20220316160659389](E:\Typora\Mysql\image-20220316160659389.png)

```mysql
#优化前,回表
select * from user where name = "Bill";

#优化后,不再回表,但不能联合索引
select id from user where name ="Bill";

#联合索引,可以查询符合条件的两个列名,不再回表
alter table user add index 'idx_name_age_position'('name','age')#创建索引
#如下图,name是有序的,age是无序的
select id,age from user where name = 'Bill'
```



![image-20220316164127450](E:\Typora\Mysql\image-20220316164127450.png)

## 最左前缀优化原则
	mysql会一直向右匹配直到遇到范围查询（>、<、between、like）就停止匹配,开始在这个范围进行update、select.比如a=3 and b=4 and c>5 and d=6 如果建立（a、b、c、d）顺序的索引,d是用不到索引的，如果建立(a、b、d、c)的索引则都可以用到,a、b、d的顺序可以任意调整。


	=和in可以乱序，比如a=1 and b=2 and c=3建立（a、b、c）索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

# 分析器（Explain）
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
	derived_在from字句后的子查询。Mysql会将结果存放在一个临时表中,也称为派生类

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

## extra
	当value为Using filesort时，将用外部排序，这种情况下需要考虑使用索引优化
	当value为Using index时，建立了索引

## 面试题：mysql在什么时候会选择使用全表索引

	在使用不等于（!=或者<>）的时候
	当范围查询的效率没有全表查询的效率高时
	is null,is not null
	like以通配符开头('$abc...')('%abc...')
	少用or或in，用它查询时，mysql不一定使用索引

## 索引总结图
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

like KK%相当于=常量，%KK和%KK%相当于范围




# 底层原理
Sql的底层是B+树

## 内部结构
	MySQL 大体可以分为Server层和存储引擎层两部分

![image-20220312144139527](E:\Typora\Mysql\image-20220312144139527.png)

## Server层
	包括连接器、查询缓存、分析器、优化器、执行器等,涵盖 MySQL 的大多数核心服务功能,以及所有的内置函数
## Store层
	存储引擎层负责数据的存储和提取。
## 连接器
连接到这个数据库上，这时候接待你的就是连接器。连接器负责跟客户端建立连接、获取权限、维持和管理连接。
```
[root@192 ~]# mysql ‐h host[数据库地址] ‐u root[用户] ‐p root[密码] ‐P 3306
```
## 分析器
```
如果没有命中查询缓存,就要开始真正执行语句了。MySQL需要知道你要做什么,因此需要对 SQL 语句做解析。
分析器先会做“词法分析”,你输入的是由多个字符串和空格组成的一条 SQL 语句,MySQL需要识别出里面的字符串。
从你输入的"select"这个关键字识别出来,这是一个查询语句。把字符串“ID”识别成“列 ID”
做完了这些识别以后,就要做“语法分析”。根据词法分析的结果,语法分析器会根据语法规则,判断是否满足语法。
```

![image-20220312145746530](E:\Typora\Mysql\image-20220312145746530.png)

## 优化器
	优化器是在表里面有多个索引的时候,决定使用哪个索引
## 执行器
	开始执行的时候,要先判断一下你对这个表 T 有没有执行查询的权限，如果没有，就会返回没有权限的错误。
# bin-log归档
	删库是不需要跑路的，因为我们的SQL执行时，会将sql语句的执行逻辑记录在我们的bin-log当中
## 特点
	Binlog在MySQL的Server层实现（引擎共用）
	Binlog为逻辑日志,记录的是一条语句的原始逻辑
	Binlog不限大小,追加写入,不会覆盖以前的日志
## 归档操作
```mysql
# 从bin‐log恢复全部数据
/usr/local/mysql/bin/mysqlbinlog ‐‐no‐defaults /usr/local/mysql/data/binlog/mysql‐bin.000001
/mysql ‐uroot ‐p tuling(数据库名)
# 恢复指定位置数据
/usr/local/mysql/bin/mysqlbinlog ‐‐no‐defaults ‐‐start‐position="408" ‐‐stop‐position="731"
/usr/local/mysql/data/binlog/mysql‐bin.000001 |mysql ‐uroot ‐p tuling(数据库)
# 恢复指定时间段数据
/usr/local/mysql/bin/mysqlbinlog ‐‐no‐defaults /usr/local/mysql/data/binlog/mysql‐bin.000001 ‐‐stop‐date= "2018‐03‐02 12:00:00" ‐‐start‐date= "2019‐03‐02 11:55:00"|mysql ‐uroot ‐p test(数据库)
```
# 优化
## 强制运行索引
```mysql
explain select * from employees force index(idx_name_age_position) where name >'LeiLei' AND  age =22 AND position = 'manager';
```
### 比较运行时间
```mysql
set global query_cache_size=0;#将运行过的查询缓存清零
set global query_cache_type=0;

select * from employees force index(idx_name_age_position) where name >'LeiLei' AND  age =22 AND position = 'manager';#观察运行时间,得出效率
select * from employees  where name >'LeiLei' AND  age =22 AND position = 'manager';
```

## 索引下推
	在索引遍历过程中,对索引中包含的所有字段先做判断,过滤掉不符合条件的记录后再回表,可以有效的减少回表次数。

### 面试题：为什么范围查找没有用索引下推，而like却能够用到
	Mysql认为范围查找过滤的结果集更大,like KK%在绝大数情况来看,过滤后的结果集较小。                

## Mysql优化器
```mysql
#开启trace,用完要关闭，不然浪费性能
set session optimizer_trace="enabled=on",end_markers_in_json=on; 
#分析执行计划
select * from employees where name > 'a' order by position;
select * from information_schema.OPTIMIZER_TRACE;
```

## Order by

Case1:

![image-20220313115214847](E:\Typora\Mysql\image-20220313115214847.png)

	利用最左前缀法则:中间字段不能断,因此查询用到了name索引



Case2:

![image-20220313120033147](E:\Typora\Mysql\image-20220313120033147.png)

	key_len=74,查询使用了name索引,由于用了position进行排序,跳过了age,出现了Using filesort



Case3:

![image-20220313120305663](E:\Typora\Mysql\image-20220313120305663.png)

查找只用到索引 name,age和position用于排序,因此查询用到了index查询



Case4：

![image-20220313120531795](E:\Typora\Mysql\image-20220313120531795.png)

和Case 3中explain的执行结果一样，但是出现了Using filesort，因为索引的创建顺序为 name,age,position，但是排序的时候age和position颠倒位置了。



Case5：

![image-20220313120740457](E:\Typora\Mysql\image-20220313120740457.png)



Case6：

![image-20220313120922852](E:\Typora\Mysql\image-20220313120922852.png)

	虽然排序的字段列与索引顺序一样,且order by默认升序,这里position desc变成了降序,导致与索引的排序方式不同,从而产生Using filesort

Case7:

![image-20220313121132041](E:\Typora\Mysql\image-20220313121132041.png)

	对于排序来说，多个相等条件也是范围查询,故产生了Using filesort

Case8：

![image-20220313121312683](E:\Typora\Mysql\image-20220313121312683.png)

	当使用*进行匹配时，此时并没有索引，故可以使用覆盖索引进行优化

Case8解决方案：

![image-20220313121516606](E:\Typora\Mysql\image-20220313121516606.png)



```
能用覆盖索引尽量用覆盖索引
MySQL支持两种方式的排序filesort和index,Using index是指MySQL扫描索引本身完成排序
order by满足两种情况会使用Using index
	1) order by语句使用索引最左前列。
	2) 使用where子句与order by子句条件列组合满足索引最左前列
尽量在索引列上完成排序,遵循索引建立（索引创建的顺序）时的最左前缀法则
	如果order by的条件不在索引列上,就会产生Using filesort
	group by与order by很类似,其实质是先排序后分组,遵照索引创建顺序的最左前缀法则。对于group
by的优化如果不需要排序的可以加上order by null禁止排序。注意,where高于having,能写在where中
的限定条件就不要去having限定了
```

## 慢sql查询
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

## 设计索引原则
	代码先行,索引后上,等到主体业务功能开发完毕,把涉及到该表相关sql都要拿出来分析之后再建立索引。


	联合索引尽量覆盖条件，设计一个或者两三个联合索引(尽量少建单值索引)，让每一个联合索引都尽量去包含sql语句里的where、order by、group by的字段，还要确保这些联合索引的字段顺序尽量满足sql查询的最左前缀原则。


	不要在小基数字段上建立索引,索引基数是指这个字段在表里总共有多少个不同的值,比如一张表总共100万行记录,其中有性别字段,其值不是男就是女,那么该字段的基数就是2
	一般建立索引,尽量使用那些基数比较大的字段,就是值比较多的字段,那么才能发挥出B+树快速二分查找的优势来。
	长字符串我们可以采用前缀索引,尽量对字段类型较小的列设计索引,比如说什么tinyint之类的,因为字段类型较小的话,占用磁盘空间也会比较小。


	对于这种varchar(255)的大字段可能会比较占用磁盘空间,可以稍微优化下,比如针对这个字段的前20个字符建立索引,就是说,对这个字段里的每个值的前20个字符放在索引树里,类似于 KEY index(name(20),age,position)。
	此时你在where条件里搜索的时候，如果是根据name字段来搜索，那么此时就会先到索引树里根据name字段的前20个字符去搜索，定位到之后前20个字符的前缀匹配的部分数据之后，再回到聚簇索引提取出来完整的name字段值进行比对。
	但是假如你要是order by name,那么此时你的name因为在索引树里仅仅包含了前20个字符,所以这个排序是没法用上索引的,group by也是同理。所以这里大家要对前缀索引有一个了解。


	where与order by冲突时优先where,一般这种时候往往都是让where条件去使用索引来快速筛选出来一部分指定的数据,接着再进行排序。因为大多数情况基于索引进行where筛选往往可以最快速度筛选出你要的少部分数据，然后做排序的成本可能会小很多。

## 分页查询优化
当数据非常大的情况下，如何优化

### 根据主键排序的
```mysql
# 优化前
select * from employees limit 90000,5;
# 优化后，改写后的SQL走了索引，条件:需要主键连续，主键不能够被删除或增加
select * from employees where id > 90000 limit 5;
```


## JOIN
### NLJ

嵌套循环连接 Nested-Loop Join

```mysql
Explain select * from t1 inner join t2 on t1.a = t2.a
```

![image-20220314152159618](E:\Typora\Mysql\image-20220314152159618.png)

	t1 10000rows,t2 100rows.先从t2表拿1row记录,再去t1表联合查询一次,因为t1表是索引,在索引树上搜索,最多搜索3次,就扫到了叶子结点,拿出来数据的时候遍历了一次,t2总共一百次联合查询t1,因为t1走了索引,故查询了100(t2)+100(t1)=200行.

#### 对于关联sql的优化
	关联字段加索引，让mysql做join操作时尽量选择NLJ算法
	小表驱动大表，如果明确知道那张表是小表，可以用straight_join固定连接驱动方式
```mysql
#mysql选择t2作为驱动表
select * from t2 straight_join t1 on t2.a;
```

#### 主键排序再优化
```mysql
Explain select * from employees e inner join(select id from employees order by name limit 90000,5) ed on e.id =e.id
```

![image-20220314114452997](E:\Typora\Mysql\image-20220314114452997.png)

	id=2的优先级较高,先执行,使用索引查找出5条数据,在对其使用笛卡尔积进行联合索引,不用担心主键不是自增长

### BNL

块的嵌套循环连接 Block Nested-Loop Join

```mysql
EXPLAIN select * from t1 inner join t2 on t1.b = t2.b;
```
#### 流程
	把t2的所有数据放入到join_buffer
	把表t1中每一行取出来,跟join_buffer中的数据做对比
	返回满足join条件的数据
#### 原理
	扫描总行数为10000(表t1的数据总量)+100(表t2的数据总量)=10100
	内存判断次数 100*10000=100,0000次
#### 规则
	当t2是一个大表,join_buffer放不下,可以选择调整join_buffer_size,亦或者分段放.
	假如,t2表有1600条数据,join buffer一次只能放800行记录,然后从t1表里取数据跟join_buffer中数据做对比得到部分结果,然后清空join_buffer,再放入剩余的800条记录,再次从t1表里取数据跟join_buffer中数据对比得到结果

### 面试题:如何选择BNL或NLJ
	如果使用上面第二条sql使用NLJ,那么扫描次数为100万次,这个是磁盘扫描.相对于磁盘扫描,BNL的内存计算会快很多.
	而对于有索引的算法,则可以优先考虑NLJ.

## IN
当B表的数据集小于A表时，in优先于exists
```mysql
select * from A where id in (select id from B)

#等价于
for(select id  from B ){
	select * from A where A.id = B.id
	}
```

## Exists
当B表的数据集大于A表时，exists优先于in
```mysql
select * from A where exists (select 1 from B where B.id = A.id)

#等价于
	for(select * from A ){
		select 1 from B where B.id = A.id
	}
```
	EXISTS(subquery)只返回TRUE或FALSE、因此子查询中的select * 也可以用select 1代替,官方说法是实际执行时会忽略SELECT清单,因此没有区别
	EXISTS子查询往往也可以用JOIN来代替

## Count
```mysql
#临时关闭mysql查询优化，为了查看sql多次执行的真实时间
set global query_cache_size=0;
set global query_cache_type=0;

Explain select count(1) from employees;
Explain select count(id) from employees;
Explain select count(name) from employees;
Explain select count(*) from employees;
```
### 有索引效率
	count(*)≈count(1)>count(字段)>count(主键 id)
	字段有索引,count（字段）走二级索引,存储数据比主键索引少,所以count（字段）>id
### 无索引效率
	count(*)≈count(1)>count(主键 id)>count(字段)
	字段没有索引,count（字段）走不了二级索引,所以count（字段）< id
### 查询维护的总行数
```mysql
#对于myisam存储引擎的表做不带where条件的count查询性能是很高的,因为myisam存储引擎的表的总行数会被存储在硬盘上,查询不需要计算
EXPLAIN select count(*) from test_myisam;
#而对于innodb存储引擎的表mysql不会存储表的总记录行数（因为有MVCC机制）,查询count需要实时计算
```

### 只查询表总行数
```mysql
show tables status like 'employees';
```
### 将总数维护到Redis里
	插入或删除表数据行的时候同时维护redis里表总行数key的计数值（用incr或decr命令）
### 增加数据库计数表
	插入或删除表数据行的时候同时维护计数表，让他们在同一个事务里操作


# 锁机制
将复杂的的多表连接交给java，单表交给mysql，它的空间很宝贵

## 分类
### 性能
	乐观锁用版本号去比对来实现锁,性能较好
```mysql
select * from t1 where version =1; 
```
	悲观锁,性能较差
### 操作类型
	读锁 和 写锁 都属于悲观锁
	读锁（共享锁，S（Shared））:针对同一份数据，多个读操作可以同时进行而不会互相影响
	写锁（排他锁，X（eXcusive））:当前写操作没有完成前，它会阻断其他写锁和读锁
### 操作粒度
#### 行锁
	开销大，加锁慢，会出现死锁;锁定粒度大,并发度高，锁冲突的概率最低，并发度最高。
	读锁会阻塞写，但是不会阻塞读。而写锁则会把读和写都阻塞。


#### 表锁
```mysql
#手动增加表锁
lock table 表名称 read(write),表名称2 read(write)
#查看表上加过的锁
show open tables;
#删除表锁
unlock tables;
```
## 间隙锁
	在Session_1下面执行 update account set name = "zhuge" where id > 8 and id < 18;则其他Session 没法在这个范围包含的所有行记录以及行记录所在的间隙里插入或删除任何数据，间隙锁是在可重复读隔离级别下才能生效。

## 临键锁
	行锁与间隙锁的组合






# 事务
每个数据库连接都有一个全局变量@@tx_isolation,表示当前的事务隔离级别

## 查看隔离级别
	select @@tx_isolation;
## 设置隔离级别
	set transaction isolation level read commit;
## 设置全局的隔离级别
	set global transaction isolation level read commit;


# Undo日志版本链 和 ReadView机制
	一行数据被多个事务依次修改后,在每个事务修改完成后,Mysql会保留修改前的数据undo回滚日志,并且用两个隐藏字段trx_id和roll_pointer把这些undo日志串联起来形成一个历史记录版本链

![image-20220315222932724](E:\Typora\Mysql\image-20220315222932724.png)

# MVCC

	多版本并发控制隔离机制（Multi-Version Concurrency Control）,在可重复读隔离级别下如何事务较高的隔离性,Mysql在读已提交和可重复读隔离级别下都实现了MVCC机制。


# BufferPool





