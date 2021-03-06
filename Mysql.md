# 数据库概述
## Why
	能够存储一些数据,并把数据保存到可掉电式存储设备中以供之后使用
## What
	将内存中的数据存储在关系型数据库中
## SQL
	结构化查询语言(Structured Query Language),专门用来与数据库通信的语言
## DB
	数据库(Database),即存储数据的“仓库”,其本质是一个文件系统。它保存了一系列有组织的数据

![image-20220416165547206](E:\Typora\Mysql\image-20220416165547206.png)

## DBMS

	数据库管理系统(Database Management System)
	是一种操纵和管理数据库的大型软件,用于建立、使用和维护数据库,对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库中表内的数据。

![image-20220416165623904](E:\Typora\Mysql\image-20220416165623904.png)

## The Relationship between DB & DBMS

![image-20220416161450268](E:\Typora\Mysql\image-20220416161450268.png)

## Common Databases

![image-20220416165008887](E:\Typora\Mysql\image-20220416165008887.png)

## RDBMS
	最古老的数据库类型,关系型数据库模型是把复杂的数据结构归结为简单的二元关系(即二维表格形式)。


## Non-RDBMS
	非关系型数据库,可看成传统关系型数据库的功能的阉割版本, 基于键值对存储数据,不需要经过SQL层的解析,性能非常高。同时,通过减少不常用的功能,进一步提高性能

### 键值型数据库
	键值型数据库通过 Key-Value 键值的方式来存储数据,其中 Key 和 Value 可以是简单的对象,也可以是复杂的对象。Key 作为唯一的标识符,优点是查找速度快,在这方面明显优于关系型数据库,缺点是无法像关系型数据库一样使用条件过滤(如 WHERE),如果你不知道去哪里找数据,就要遍历所有的键,这就会消耗大量的计算。键值型数据库典型的使用场景是作为 内存缓存。Redis 是最流行的键值型数据库。

![image-20220416173538989](E:\Typora\Mysql\image-20220416173538989.png)

### 文档型数据库
	此类数据库可存放并获取文档，可以是XML、JSON等格式。在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录。文档数据库所存放的文档，就相当于键值数据库所存放的“值”。MongoDB是最流行的文档型数据库。

### 搜索引擎数据库
	虽然关系型数据库采用了索引提升检索效率，但是针对全文索引效率却较低。搜索引擎数据库是应用在搜索引擎领域的数据存储形式，由于搜索引擎会爬取大量的数据，并以特定的格式进行存储，这样在检索的时候才能保证性能最优。核心原理是“倒排索引”。
	典型产品：Solr、Elasticsearch、Splunk 等。

###  列式数据库
	列式数据库是相对于行式存储的数据库，Oracle、MySQL、SQL Server 等数据库都是采用的行式存储（Row-based）,而列式数据库是将数据按照列存储到数据库中，这样做的好处是可以大量降低系统的I/O，适合于分布式文件系统，不足在于功能相对有限。典型产品:HBase。

![image-20220416174352634](E:\Typora\Mysql\image-20220416174352634.png)

### 图形数据库
	是一种存储图形关系的数据库。它利用了图这种数据结构存储了实体（对象）之间的关系。关系型数据用于存储明确关系的数据，但对于复杂关系的数据存储却有些力不从心。如社交网络中人物之间的关系，如果用关系型数据库则非常复杂，用图形数据库将非常简单。典型产品:Neo4J、InfoGrid等。

![image-20220416174604905](E:\Typora\Mysql\image-20220416174604905.png)

##  Rule
关系型数据库设计规则

	表具有一些特性,这些特性定义了数据在表中如何存储类似Java中 “类”的设计

### ER模型
实体-联系(entity-relationship),模型中有三个主要概念是:实体集、属性、联系集
![image-20220419212027433](E:\Typora\Mysql\image-20220419212027433.png)

	一个实体集(class)对应于数据库中的一个表（table）,一个实体（instance）则对应于数据库表中的一行(row),也称为一条记录(record)。一个属性（attribute）对应于数据库表中的一列(column)，也称为一个字段(field).

### ORM思想
对象关系映射(Object Relational Mapping)

	数据库中的一个表 <---> Java的一个类
	表中的一条数据 <---> 类中的一个对象
	表中的一个列 <----> 类中的一个属性

## 表的关联关系

### 一对一
    在实际的开发中应用不多,因为一对一可以创建成一张表。
    举例: 设计学生表 : 学号、姓名、手机号码、班级、系别、身份证号码、家庭住址、籍贯、紧急、联系人、...
    拆为两个表：两个表的记录是一一对应关系。
    基础信息表 （常用信息）：学号、姓名、手机号码、班级、系别
    档案信息表 （不常用信息）：学号、身份证号码、家庭住址、籍贯、紧急联系人、...

### 一对多
	常见实例场景： 客户表和订单表, 分类表和商品表,部门表和员工表 。
	举例:
	员工表：编号、姓名、...、所属部门
	部门表：编号、名称、简介

### 多对多
	必须创建第三个表,该表通常称为 联接表,它将多对多关系划分为两个一对多关系。将这两个表的主键都插入到第三个表
	常见实例场景: 用户-角色
	多对多关系建表原则：需要创建第三张表，中间表中至少两个字段，这两个字段分别作为外键指向各自一方的主键

![image-20220420160908821](E:\Typora\Mysql\image-20220420160908821.png)

## SQL大小写规范
	MySQL 在 Windows 环境下是大小写不敏感的
	MySQL 在 Linux 环境下是大小写敏感的
	数据库名、表名、表别名、字段名、字段别名等都小写
	SQL关键字、函数名、绑定变量等都大写

# 环境搭建
##  配置环境变量
	变量名:MYSQL_HOME
	变量值:E:\mysql-5.7.20-winx64
	path里添加:%MYSQL_HOME%\bin

## 生成data文件
	以管理员身份运行cmd
	进入E:\mysql-5.7.20-winx64\bin下
	执行命令:mysqld --initialize-insecure --user=mysql  
	在E:\mysql-5.7.20-winx64目录下生成data目录

## 启动服务
	执行命令：net start mysql 
	提示：服务名无效
	解决方法:
	执行命令:mysqld -install  即可（不需要my.ini配置文件 注意：网上写的很多需要my.ini配置文件，其实不需要my.ini配置文件也可以，我之前放置了my.ini文件，反而提示服务无法启动，把my.ini删除后启动成功了）
## 登录mysql
	E:\mysql-5.7.20-winx64\bin>mysql -u root -p
## 设置用户密码
	mysql> SET PASSWORD = '123456';
## 重置root密码
	通过任务管理器或者服务管理,关掉mysqld(服务进程) 
	通过命令行(CMD)+特殊参数开启
		mysqld mysqld--defaults-file="D:\ProgramFiles\mysql\MySQLServer5.7Data\my.ini" --skip-grant-tables
	此时,mysqld服务进程已经打开,并且不需要权限检查,mysql -uroot 无密码登陆服务器
	启动另一个客户端进行修改权限表
		use mysql;
		update user set authentication_string=password('新密码') where user='root' and Host='localhost'; 
		flush privileges; 
		通过任务管理器,关掉mysqld服务进程 
		再次通过服务管理,打开mysql服务
		即可用修改后的新密码登陆
## 残余文件的清理
	如果再次安装不成功,可以卸载后对残余文件进行清理后再安装。
	服务目录:mysql服务的安装目录
	数据目录:默认在C:\ProgramData\MySQL
## 清理注册表
	在系统的搜索框中输入 regedit
```
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\MySQL
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\Eventlog\Application\MySQL
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\MySQL
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application\MySQL
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MySQL
```
## 删除环境变量配置
	找到path环境变量,将其中关于mysql的环境变量删除,切记不要全部删除。

## 编码设置

	命令行操作sql乱码问题,修改mysql的数据目录下的my.ini配置文件
	default-character-set=utf8 #默认字符集
	[mysqld] # 大概在76行左右,在其下添加
	...
	character-set-server=utf8
	collation-server=utf8_general_ci
	重启服务
	查看编码命令
	show variables like 'character_%';
	show variables like 'collation_%';

![image-20220420204545208](E:\Typora\Mysql\image-20220420204545208.png)

# 基本命令
## 查看数据库

![image-20220420195126343](E:\Typora\Mysql\image-20220420195126343.png)

	“information_schema”是 MySQL 系统自带的数据库,主要保存 MySQL 数据库服务器的系统信息,
	比如数据库的名称、数据表的名称、字段名称、存取权限、数据文件 所在的文件夹和系统使用的文件夹,等等
	“performance_schema”是 MySQL 系统自带的数据库,可以用来监控 MySQL 的各类性能指标。
	“sys”数据库是 MySQL 系统自带的数据库,主要作用是以一种更容易被理解的方式展示 MySQL 数据库服务器的各类性能指标,帮助系统管理员和开发人员监控 MySQL 的技术性能。
	“mysql”数据库保存了 MySQL 数据库服务器运行时需要的系统信息,比如数据文件夹、当前使用的字符集、约束检查信息，等等


## 创建数据库

![image-20220420195215840](E:\Typora\Mysql\image-20220420195215840.png)

## 导入数据库

![image-20220421200919677](E:\Typora\Mysql\image-20220421200919677.png)


## 使用数据库
	use 数据库名;
## 查看某个库的所有表格
	show databases;
## 创建新的表格

![image-20220420200217864](E:\Typora\Mysql\image-20220420200217864.png)

## 查看一个表的数据
	show tables; #要求前面有use语句
## 添加一条记录

![image-20220420200309567](E:\Typora\Mysql\image-20220420200309567.png)

	insert into 表名称 values(值列表);
## 查看表的创建信息

![image-20220420200339554](E:\Typora\Mysql\image-20220420200339554.png)

	select * from 数据库表名称;

## 查看数据库的创建信息

![image-20220420200422935](E:\Typora\Mysql\image-20220420200422935.png)

	上面的结果显示tencent_test数据库也不支持中文，字符集默认是latin1
## 删除表格

![image-20220420201856517](E:\Typora\Mysql\image-20220420201856517.png)

## 删除数据库

![image-20220420201908275](E:\Typora\Mysql\image-20220420201908275.png)

# SELECT
## 基本语句
```mysql
SELECT 1; #没有任何子句
SELECT 9/2; #没有任何子句,用于运算
```
### SELECT ... FROM
```mysql
#SELECT 标识选择哪些列,FROM 标识从哪个表中选择
SELECT employee_id,last_name,salary FROM departments;
```
![image-20220421201939254](E:\Typora\Mysql\image-20220421201939254.png)
### 列的别名
```mysql
#给列名起别名的三种方式
SELECT employee_id emp_id,last_name AS lname,salary*12 "annual_sal" FROM employees;
```

![image-20220421204326542](E:\Typora\Mysql\image-20220421204326542.png)

### 去除重复行
```mysql
#当需要查询员工表中一共有哪些部门id
SELECT DISTINCT department_id FROM employees;
```
![image-20220421204759011](E:\Typora\Mysql\image-20220421204759011.png)

### 空值参与运算
```mysql
#在列值 奖金率有NULL值的情况下，引入IF将NULL值的数据看为0
select first_name,salary "月工资",salary*(1+IFNULL(commission_pct,0))*12 "年工资",commission_pct from employees;
#这样就不会出现年终奖为NULL的情况了
```

![image-20220421211410015](E:\Typora\Mysql\image-20220421211410015.png)

### 飘号 ` `
	为了保证表中的字段、表名等没有和保留字、数据库系统或常用方法冲突，使用一对``（飘号）引起来

![image-20220422152613529](E:\Typora\Mysql\image-20220422152613529.png)

### 显示表结构

![image-20220422152950686](E:\Typora\Mysql\image-20220422152950686.png)

### WHERE
将不满足条件的行过滤掉,WHERE子句紧随 FROM子句
```mysql
#返回在90号部门工作的所有的员工信息
select * from employees where department_id = 90;
```
![image-20220422153645940](E:\Typora\Mysql\image-20220422153645940.png)

## 运算符
用于数学运算，其可以连接运算符前后的两个数值或表达式，对数值或表达式进行运算
### 加法与减法运算符

| 运算符 | 名称       | 作用                     | 示例       |
| ------ | ---------- | ------------------------ | ---------- |
| +      | 加法运算符 | 计算两个值或表达式的和   | SELECT A+B |
| -      | 减法运算符 | 计算两个值或表达式的差   | SELECT A-B |
| *      | 乘法运算符 | 计算两个值或表达式的乘积 | SELECT A*B |
| /或DIV | 除法运算符 | 计算两个值或表达式的商   | SELECT A/B |
| %或MOD | 求余运算符 | 计算两个值或表达式的余数 | SELECT A%B |
```mysql
SELECT 100, 100 + 0, 100 - 0, 100 + 50, 100 + 50 -30, 100 + 35.5, 100 --35.5 FROM dual;
```

![image-20220422172914733](E:\Typora\Mysql\image-20220422172914733.png)

	在MySQL中+只表示数值相加。如果遇到非数值类型,先尝试转成数值,如果转失败,就按0计算.MySQL中字符串拼接要使用字符串函数CONCAT()实现.

### 乘法与除法运算符
	一个数乘以整数1和除以整数1后仍得原数
	一个数乘以浮点数1和除以浮点数1后变成浮点数,数值与原数相等
	一个数除以整数后,不管是否能除尽,结果都为一个浮点数
	一个数除以另一个数,除不尽时,结果为一个浮点数,并保留到小数点后4位
	乘法和除法的优先级相同,进行先乘后除操作与先除后乘操作,得出的结果相同
	在数学运算中,0不能用作除数,在MySQL中,一个数除以0为NULL

### 求模（求余）运算符
```mysql
#筛选出employee_id是偶数的员工
SELECT * FROM employees WHERE employee_id MOD 2 = 0;
```
### 比较运算符
	用来对表达式左边的操作数和右边的操作数进行比较，比较的结果为真则返回1，比较的结果为假则返回0，其他情况则返回NULL。

| 运算符 | 名称               | 作用                                                         | 示例                            |
| ------ | ------------------ | ------------------------------------------------------------ | ------------------------------- |
| =      | 等于运算符         | 判断两个值、字符串或表达式是否相等                           | select C from table where A=B   |
| <=>    | 安全等于运算符     | 安全地判断两个值、字符串或表达式是否相等                     | select C from table where A<=>B |
| !=     | 不等于运算符（<>） | 判断两个值、字符串或表达式是否不相等                         | select C from table where A!=B  |
| <      | 小于运算符         | 判断前面的值、字符串或表达式是否小于后面的值、字符串或表达式 | select C from table where A<B   |
| <=     | 小于等于运算符     | 判断前面的值、字符串或表达式是否小于等于后面的值、字符串或表达式 | select C from table where A<=B  |
| >      | 大于运算符         | 判断前面的值、字符串或表达式是否大于后面的值、字符串或表达式 | select C from table where A>B   |
| >=     | 大于等于运算符     | 判断前面的值、字符串或表达式是否大于等于后面的值、字符串或表达式 | select C from table where A>=B  |

```mysql
#只要有null参与运算,结果就为null
SELECT 1= NULL,NULL=NULL FROM DUAL;
```
![image-20220424153048474](E:\Typora\Mysql\image-20220424153048474.png)

### 为NULL而生的安全运算符
```mysql
SELECT 1 <=> '1', 1 <=> 0, 'a' <=> 'a', (5 + 3) <=> (2 + 6), '' <=> NULL,NULL<=> NULL FROM dual;
```

<img src="E:\Typora\Mysql\image-20220424160718368.png" alt="image-20220424160718368"  />

### 不使用安全运算符

![image-20220424160947688](E:\Typora\Mysql\image-20220424160947688.png)

### 使用安全运算符的Where子句

![image-20220424161636754](E:\Typora\Mysql\image-20220424161636754.png)

### 不使用安全运算符

![image-20220424161735884](E:\Typora\Mysql\image-20220424161735884.png)



### 非符号类型的运算符

| 运算符      | 名称             | 作用                                     | 示例                                         |
| ----------- | ---------------- | ---------------------------------------- | -------------------------------------------- |
| IS NOT NULL | 不为空运算符     | 判断值、字符串或表达式是否不为空         | SELECT C from table where A IS NOT NULL      |
| LEAST       | 最小值运算符     | 在多个值中返回最小值                     | select C from table where A LEAST （A，B）   |
| GREATEST    | 最大值运算符     | 在多个值中返回最大值                     | select C from table where A GREATEST（A，B） |
| BETWEEN AND | 两值之间的运算符 | 判断一个值是否在两个值之间               | select C from table where C BETWEEN A AND B  |
| IS NULL     | 为空运算符       | 判断一个值、字符串或表达式是否为空       | select C from table where A IS NULL          |
| IN          | 属于运算符       | 判断一个值是否为列表中的任意一个值       | select C from table where A in（A，B）       |
| NOT IN      | 不属于运算符     | 判断一个值是否不是一个列表中的任意一个值 | select C from table where A not in（A，B）   |
| LIKE        | 模糊匹配运算符   | 判断一个值是否符合模糊匹配规则           | select C from table where A not like B       |
| REGEXP      | 正则表达式运算符 | 判断一个值是否符合正则表达式的规则       | select C from table where A not  REGEXP B    |
| RLIKE       | 正则表达式运算符 | 判断一个值是否符合正则表达式的规则       | select C from table where A not  RLike B     |

### 使用Least根据名称或单词长度比较排序

![image-20220424163809098](E:\Typora\Mysql\image-20220424163809098.png)

![image-20220424163829595](E:\Typora\Mysql\image-20220424163829595.png)

### 范围查找
```mysql
SELECT last_name,salary FROM employees WHERE salary BETWEEN 2500 AND 3500;
```

![image-20220424164102340](E:\Typora\Mysql\image-20220424164102340.png)

### 离散查找
IN运算符用于判断给定的值是否是IN列表中的一个值
```mysql
SELECT 'a' IN ('a','b','c'), 1 IN (2,3), NULL IN ('a','b'), 'a' IN ('a', NULL); 
```

![image-20220424164411096](E:\Typora\Mysql\image-20220424164411096.png)

### 模糊查询
	LIKE运算符主要用来匹配字符串，通常用于模糊匹配，如果满足条件则返回1，否则返回0。
	“%”：匹配0个或多个字符
	“_”：只能匹配一个字符

![image-20220424164707399](E:\Typora\Mysql\image-20220424164707399.png)

![image-20220424164730644](E:\Typora\Mysql\image-20220424164730644.png)

![image-20220424165004436](E:\Typora\Mysql\image-20220424165004436.png)

```mysql
# 练习：查询第二个字符是_且第三个字符是'a'的员工信息
select last_name from employees where last_name like "_\_a%"
```

### REGEXP运算符
	‘^’匹配以该字符后面的字符开头的字符串
	‘$’匹配以该字符前面的字符结尾的字符串
	‘.’匹配任何一个单字符
	“[...]”匹配在方括号内的任何字符。例如，“[abc]”匹配“a”或“b”或“c”。为了命名字符的范围，使用一个‘-’。“[a-z]”匹配任何字母，而“[0-9]”匹配任何数字。
	‘*’匹配零个或多个在它前面的字符。例如,“x*”匹配任何数量的‘x’字符，“[0-9]*”匹配任何数量的数字，而“*”匹配任何数量的任何字符

```mysql
SELECT 'shkstart' REGEXP '^s', 'shkstart' REGEXP 't$', 'shkstart' REGEXP 'hk';
```

### 逻辑运算符
逻辑运算符主要用来判断表达式的真假，在MySQL中，逻辑运算符的返回结果为1、0或者NULL

| 运算符     | 作用     | 示例           |
| ---------- | -------- | -------------- |
| NOT 或 !   | 逻辑非   | select not a   |
| AND  或 && | 逻辑与   | select a && b  |
| OR 或 \|\| | 逻辑或   | select a or b  |
| XOR        | 逻辑异或 | select a XOR b |

#### 规则
	OR可以和AND一起使用,但是在使用时要注意两者的优先级,由于AND的优先级高于OR,因此先对AND两边的操作数进行操作,再与OR中的操作数结合

#### 非运算符

逻辑非（NOT或!）当给定的值为0时返回1；当给定的值为非0值时返回0； 当给定的值为NULL时，返回NULL

![image-20220425144723518](E:\Typora\Mysql\image-20220425144723518.png)

#### 与运算符
逻辑与（AND或&&）给定的所有值均为非0值，且都不为NULL时，返回1；当给定的一个值或者多个值为0时则返回0；否则返回NULL

![image-20220425145332173](E:\Typora\Mysql\image-20220425145332173.png)

#### 或运算符
逻辑或（OR或||）给定的值都不为NULL，并且任何一个值为非0值时，则返回1，否则返回0；当一个值为NULL，并且另一个值为非0值时，返回1，否则返回NULL；当两个值都为NULL时，返回NULL

![image-20220425150526347](E:\Typora\Mysql\image-20220425150526347.png)

```mysql
#查询基本薪资不在9000-12000之间的员工编号和基本薪资
select employee_id,salary from employees where (salary>12000 or salary <9000);
#法二
select employee_id,salary from employees where not(salary>9000 and salary<=12000);
```

#### 异或运算符
逻辑异或（XOR）是当给定的值中任意一个值为NULL时，则返回NULL；如果两个非NULL的值都是0或者都不等于0时，则返回0；如果一个值为0，另一个值不为0时，则返回1

#### 位运算符
位运算符是在二进制数上进行计算的运算符。位运算符会先将操作数变成二进制数，然后进行位运算，最后将计算结果从二进制变回十进制数

| 运算符 | 作用     | 示例           |
| ------ | -------- | -------------- |
| &      | 位AND    | select A and B |
| \|     | 位OR     | select A \| B  |
| ^      | 位XOR    | select A ^ B   |
| ~      | 按位取反 | select A & ~B  |
| >>     | 按位右移 | select A >> 2  |
| <<     | 按位左移 | select B << 2  |

![image-20220425153612597](E:\Typora\Mysql\image-20220425153612597.png)

##### 按位取反
按位取反（~）运算符将给定的值的二进制数逐位进行取反操作，即将1变为0，将0变为1
```mysql
SELECT 10 & ~1;
SELECT 1 & ~1;
```

![image-20220426154115240](E:\Typora\Mysql\image-20220426154115240.png)


#### 运算符优先级

| 优先级 | 运算符                                                     |
| ------ | ---------------------------------------------------------- |
| 1      | ( )                                                        |
| 2      | !                                                          |
| 3      | —(负号) 和 ~（按位取反）                                   |
| 4      | ^（位XOR）                                                 |
| 5      | * , / 和（DIV） ,  %和MOD                                  |
| 6      | - 和 +                                                     |
| 7      | << 与 >>                                                   |
| 8      | &                                                          |
| 9      | \|                                                         |
| 10     | =（比较运算符），<==>,>=,>,<=,<,<>,!=,IS,LIKE,REGEXP 和 IN |
| 11     | BETWEEN,CASE,WHEN,THEN和ELSE                               |
| 12     | NOT                                                        |
| 13     | && , AND                                                   |
| 14     | \|\|,OR,XOR                                                |
| 15     | := , =                                                     |


## 排序
### 模板
```mysql
SELECT * FROM employees ORDER BY hire_date [DESC][ASC];
```
### 单列排序
```mysql
SELECT last_name, job_id,department_id,hire_date
FROM employees
ORDER BY hire_date ASC;
```
### 多列排序
#### 规则
	对多列进行排序的时候,首先排序的第一列必须有相同的列值,才会对第二列进行排序。
	如果第一列数据中所有值都是唯一的,将不再对第二列进行排序

#### 示例代码
```mysql
#因为是多列排序，故department_id列后未添加降序or升序，默认为升序
SELECT last_name,department_id,salary FROM employees ORDER BY department_id,salary ASC;
#添加后，假使第一列(department_id)有相同的，则按照下一列（salary）进行升序排序
SELECT last_name,department_id,salary FROM employees ORDER BY department_id DESC,salary ASC;
```

![image-20220428161501301](E:\Typora\Mysql\image-20220428161501301.png)

## 分页
将数据库中的结果集，一段一段显示出来需要的条件

### 优点
	约束返回结果的数量可以减少数据表的网络传输量,也可以提升查询效率.如果我们知道返回结果只有1条,就可以使用 LIMIT 1.告诉 SELECT 语句只需要返回一条记录即可.这样的好处就是 SELECT 不需要扫描完整的表,只需要检索到一条符合条件的记录即可返回。

### 场景
	场景一:查询返回的记录太多了,查看起来很不方便,怎么样能够实现分页查询呢？
	场景二:表里有 4 条数据,我们只想要显示第 2、3 条数据怎么办呢？

### 模板
	LIMIT[位置偏移量] 行数
	位置偏移量:开头是从第几个数开始的
	行数:指示返回的记录条数

### 规则
	LIMIT 子句必须放在整个 SELECT 语句的最后！

### 实例代码
```mysql
#前10条记录
SELECT * FROM 表名 LIMIT 0,10;
SELECT * FROM 表名 LIMIT 10;

#第11至20条记录
SELECT * FROM 表名 LIMIT 10,10;

#第21至30条记录：
SELECT * FROM 表名 LIMIT 20,10;

#获取从第5条记录开始后面的3条记录
SELECT * FROM 表名 LIMIT 3 OFFSET 4;
SELECT * FROM table LIMIT 4,3;

```
### 分页显示公式
```mysql
# 每页显示pageSize条记录，此时显示第pageNo页:
LIMIT (pageNo-1)*pageSize,pagSize
```

## 多表查询
也称为关联查询，指两个或更多个表一起完成查询操作

### 规则
	这些一起查询的表之间是有关系的,这个关联字段可能建立了外键，也可能没有建立外键

### 案例
```mysql
#查询员工的姓名及其所归属的部门名称
SELECT last_name,department_name FROM employees,departments;
```
	本意是想找到员工所对应的部门名称，结果员工在每个部门都有所归属,故此查询错误
![image-20220429195012279](E:\Typora\Mysql\image-20220429195012279.png)

我们把上述多表查询中出现的问题称为：笛卡尔积的错误

![image-20220429194836005](E:\Typora\Mysql\image-20220429194836005.png)

### 笛卡尔积
	笛卡尔积也称为交叉连接英文是 CROSS JOIN 。在 SQL 中也是使用 CROSS JOIN 表示交叉连接。它的作用就是可以把任意表进行连接，即使这两张表不相关。
	假如第一张表有10个数据,第二张表有20个数据,笛卡尔积之后则会有10*20=200个数据
#### 产生笛卡尔积错误的条件
	省略多个表的连接条件（或关联条件）
	连接条件（或关联条件）无效
	所有表中的所有行互相连接
#### 如何避免笛卡尔积
	可以在 WHERE 加入有效的连接条件
```mysql
SELECT table1.column, table2.column
FROM table1, table2
WHERE table1.column1 = table2.column2; 
```
### 案例（修改后）
```mysql
SELECT last_name,department_name FROM employees,departments WHERE employees.department_id = departments.department_id;
#在表中有相同列时，在列名之前加上表名前缀
```

### 等值连接

![image-20220430095700386](E:\Typora\Mysql\image-20220430095700386.png)

```mysql
SELECT employees.department_id,departments.department_id from employees,departments where employees.department_id=departments.department_id;
```

![image-20220430101130005](E:\Typora\Mysql\image-20220430101130005.png)

### 多个连接条件

#### 规则
	多个表中有相同列时，必须在列名之前加上表名前缀
	在不同表中具有相同列名的列可以用 表名 加以区分

#### 示例代码
```mysql
SELECT departments.manager_id,departments.department_id,departments.location_id,employees.first_name,employees.salary FROM emp_details_view,departments,employees WHERE emp_details_view.department_id=departments.department_id AND departments.department_id=employees.department_id;
```
### 表的别名
使用别名可以简化查询，列名前使用表名前缀可以提高查询效率。
#### 规则
	对于数据库中表记录的查询和变更，只要涉及多个表，都需要在列名前加表的别名进行限定。
	如果使用了表的别名，在查询字段中、过滤条件中就只能使用别名进行代替，不能使用原有的表名，否则就会报错
#### 正例
```mysql
select t1.name from table_first as t1 , table_second as t2 where t1.id=t2.id;
```
#### 反例
	在某业务中，由于多表关联查询语句没有加表的别名（或表名）的限制，正常运行两年后，最近在 某个表中增加一个同名字段，在预发布环境做数据库变更后，线上查询语句出现出
```mysql
1052 异常：Column 'name' in field list is ambiguous(模棱两可的)
```

#### 示例代码

```mysql
SELECT e.employee_id,e.last_name,e.department_id,d.department_id,d.location_id FROM employees e,departments d WHERE e.department_id=d.department_id;
```
### 非等值连接
#### 案例
查询雇员的工资程度，其工资范围应当在1000~40000

![image-20220430113118904](E:\Typora\Mysql\image-20220430113118904.png)

```mysql
SELECT e.last_name ,e.salary,j.grade_level,j.lowest_sal,j.highest_sal FROM employees e,job_grades j WHERE e.salary BETWEEN j.lowest_sal AND j.highest_sal;
```

<img src="E:\Typora\Mysql\image-20220430115328004.png" alt="image-20220430115328004"  />

### 自连接 
查询使用的是自己的表连接自己的表，而非自连接使用了多张不同的表互相连接
#### 案例

	table1和table2本质上是同一张表,只是用取别名的方式虚代表不同的意义。然后两个表再进行内外连接查询

![image-20220501105942296](E:\Typora\Mysql\image-20220501105942296.png)

	在每个WORKER上层都有一个MANAGER,并对应着MANAGER_ID,而在MANAGER表中,每个经理就理所当然的变成了表中的成员,对应着EMPLOYEES_ID,在前者表中的MANAGER_ID等于后者的EMPLOYEES_ID
	查询employees表，返回“Xxx works for Xxx”
```mysql
SELECT CONCAT(worker.last_name,' works for ',manager.last_name) from employees worker,employees manager where worker.manager_id = manager.employee_id;
```

#### 案例2
	查询出last_name 为 'Chen' 的员工的manager的信息，除了查询满足条件的记录以外，外连接还可以查询某一方不满足条件的记录。
```mysql
SELECT manager.last_name,manager.department_id FROM employees worker,employees manager WHERE (worker.last_name = manager.last_name AND manager.last_name like '%Chen%');
```

![image-20220501115946909](E:\Typora\Mysql\image-20220501115946909.png)
### 内连接
	INNER JOIN,合并具有同一列的两个以上的表的行,结果中不包含两表不匹配的行
#### 模板
	SELECT 字段列表 FROM A表 INNER JOIN B表 ON 关联条件 WHERE 等其他子句
#### 例题一

![image-20220501150315381](E:\Typora\Mysql\image-20220501150315381.png)

```mysql
select e.employee_id,e.last_name,e.department_id,d.department_id,d.location_id from employees e INNER JOIN departments d ON (e.department_id=d.department_id);
```
![image-20220501151939729](E:\Typora\Mysql\image-20220501151939729.png)

#### 例题二

![image-20220501151731781](E:\Typora\Mysql\image-20220501151731781.png)

```mysql
FROM employees e
JOIN departments d
ON d.department_id = e.department_id
JOIN locations l
ON d.location_id = l.location_id;
```

### 外连接
	两个表在连接过程中除了返回满足连接条件的行以外,还返回左（或右）表中不满足条件的行，没有匹配的行时，结果表中相应的列为NULL
	左外连接,则连接条件中左边的表也称为主表,右边的表称为从表
	右外连接,则连接条件中右边的表也称为主表,左边的表称为从表

### Oracle 中使用(+)创建外连接
	在SQL92中采用(+)代表从表所在的位置,即左或右外连接中,(+)表示哪个是从表
	Oracle 对 SQL92 支持较好,而 MySQL 则不支持 SQL92 的外连接
	在 SQL92 中,只有左外连接和右外连接，没有满（或全）外连接

```mysql
#左外连接
SELECT last_name,department_name
FROM employees ,departments
WHERE employees.department_id = departments.department_id(+);
#右外连接
SELECT last_name,department_name
FROM employees ,departments
WHERE employees.department_id(+) = departments.department_id;
```

### MYSQL 中使用（JOIN...ON）实现多表查询
	SQL99 采用的这种嵌套结构非常清爽、层次性更强、可读性更强
	关键字JOIN、INNER JOIN、CROSS JOIN 的含义相同,都表示内连接
	可以使用 ON 子句指定额外的连接条件,这个连接条件是与其他条件分开的

```mysql
SELECT table1.column, table2.column,table3.column FROM table1
JOIN table2 ON table1 和 table2 的连接条件
JOIN table3 ON table2 和 table3 的连接条件
```
#### LEFT JOIN

```mysql
#当左边的A表中的一行为NULL时，右边所对应的数据将不会被显示
SELECT 字段列表
FROM A表 LEFT JOIN B表
ON 关联条件
WHERE 等其他子句;
```

![image-20220506183918572](E:\Typora\Mysql\image-20220506183918572.png)

#### RIGHT JOIN

```mysql
#当右边的B表中的一行为NULL时，左边所对应的数据将不会被显示
SELECT 字段列表
FROM A表 RIGHT JOIN B表
ON 关联条件
WHERE 等其他子句;
```

![image-20220506183853732](E:\Typora\Mysql\image-20220506183853732.png)

#### 七重JOIN

![image-20220506190423161](E:\Typora\Mysql\image-20220506190423161.png)

#### 面试题：WHERE和ON的区别

	ON条件是在生成临时表时使用的条件，他不管ON中的条件是否为真，都会返回左边表中的记录
	WHERE条件是在临时表生成之后，再对临时表进行过滤的条件



#### 满外连接(FULL OUTER JOIN)
满外连接的结果 = 左右表匹配的数据 + 左表没有匹配到的数据 + 右表没有匹配到的数据

	SQL99是支持满外连接的.使用FULL JOIN 或 FULL OUTER JOIN来实现
	MySQL不支持FULL JOIN,但是可以用 LEFT JOIN UNION RIGHT join代替

#### 嵌套逻辑
```java
//三层for循环，只有在同时满足条件一和条件二才可以执行
	for t1 in table1:
		for t2 in table2:
			if condition1:
				for t3 in table3:
					if condition2:
						output t1 + t2 + t3
```

### UNION
	返回两个查询的结果集的并集，去除重复记录。给出多条SELECT语句，并将它们的结果组合成单个结果集。合并时，两个表对应的列数和数据类型必须相同，并且相互对应。各个SELECT语句之间使用UNION或UNION ALL关键字分隔。
```mysql
SELECT column,... FROM table1
UNION [ALL]
SELECT column,... FROM table2
```
```mysql
# 查询部门编号>90或邮箱包含a的员工信息
select * from employees where department_id >90
UNION
select * from employees where email like "%x%";
```

![image-20220506185323991](E:\Typora\Mysql\image-20220506185323991.png)

#### UNION ALL

	返回两个查询的结果集的并集。对于两个结果集的重复部分，不去重



### 面试题：UNION和CONCATE的区别
	UNION会去重复后合并，而CONCAT不去重直接合并，所以CONCAT速度比较快。如果不需要去重的话，选择使用Concat做合并性能较高。


### USING
使用using关键字来简化连接查询
#### 规则
	查询必须是等值连接
	等值连接中的列必须具有相同的名称和数据类型。
```mysql
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
USING (department_id);
```




## 单行函数
## 聚合函数
## 子查询
# D?L
	DDL:Data Definition Languages、数据定义语言 CREATE \ DROP \ ALTER \ DROP \ RENAME \ TRUNCATE
	DML:Data Manipulation Language、数据操作语言 INSERT \ DELETE \ UPDATE \ SELECT(重)
	DCL:Data Control Language、数据控制语言 COMMIT \ ROLLBACK \ SAVEPOINT \ GRANT \ REVOKE
## 创建和管理表


## 数据处理之增删改
## MySQL数据类型精讲
## 约束
# 其它
## 视图
## 存储过程与函数
## 变量、流程控制与游标
## 触发器
# MySQL8 新特性


# Linux & MySQL
## MySQL的数据目录
## 用户与权限管理
## 逻辑架构
## 存储引擎
## InnoDB数据页结构
# 索引及调优
## 索引
## 性能分析工具的使用
## 索引优化与SQL优化
## 数据库的设计规范
## 数据库其他调优策略
# 事务
## 事务基础知识
## MySQL事务日志
## 锁
## 多版本并发控制（MVCC）

# 日志与备份篇
## 其它数据库日志
## 主从复制
## 数据库备份与恢复




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
利用数据结构,将数据排好序,以帮助Mysql高效获取

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
	如果表位置indx已经包含密钥,则无需插入它
	否则,如果表位置indx为空,则在其中插入键
	其他碰撞。设置 indx =（indx + 1）mod M.
	如果 indx == H（K）,则表已满！就只能做哈希表的扩容了
	因此,线性探测基本上是在发生碰撞时对空槽进行线性搜索。


### R-B Tree
红黑树,又称自平衡二叉查找树,是在计算机科学中用到的一种数据结构,典型是实现关联数组
#### 规则
	根节点必为黑色
	节点分为红色或者黑色
	叶子节点都为黑色,且为null

![image-20220316102956191](E:\Typora\Mysql\image-20220316102956191.png)


	优点：红黑树能够以O(log2(N))的时间复杂度进行搜索、插入、删除操作。此外,任何不平衡都会在3次旋转之内解决。
	缺点：在面对数据量庞大的数据时,无法判断其层数,会造成大量的IO


# 存储引擎
## Myisam
只支持表锁,高并发场景下会出现严重的问题

![image-20220316105355803](E:\Typora\Mysql\image-20220316105355803.png)

	.frm_数据库存的表的结构
	.MYD_数据库表中存的数据
	.MYI_数据库表的索引文件

## InnoDB
按照B+树组织的一个索引结构文件,支持行锁

![image-20220316105811214](E:\Typora\Mysql\image-20220316105811214.png)

	.frm_数据库存的表的结构
	.ibd_数据库的表数据文件



## 聚集索引
叶子节点包含了完整的数据结构,被Innodb所应用
![image-20220310193253048](E:\Typora\Mysql\image-20220310193253048.png)

## 面试题：B+树是如何有效查询的
	以查询25为例
	第一次磁盘I/O:访问根结点[15、56],发现25小于 [15、56] 中的 56,则访问根结点的第一个孩子结点
	第二次磁盘I/O:访问根结点[15、20、49],发现25大于20且小于49,则访问当前结点的第三个孩子结点 [20、30].
	第三次磁盘I/O:访问叶子结点 [20、30],顺序遍历结点内部,找到要查找的元素 25.



## 稀疏索引

索引文件和数据文件是分离的,被MyISAM所应用
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
	mysql会一直向右匹配直到遇到范围查询（>、<、between、like）就停止匹配,开始在这个范围进行update、select.比如a=3 and b=4 and c>5 and d=6 如果建立（a、b、c、d）顺序的索引,d是用不到索引的,如果建立(a、b、d、c)的索引则都可以用到,a、b、d的顺序可以任意调整。


	=和in可以乱序,比如a=1 and b=2 and c=3建立（a、b、c）索引可以任意顺序,mysql的查询优化器会帮你优化成索引可以识别的形式

# 分析器（Explain）
可以模拟优化器执行SQL语句,分析你的查询语句或是结构的性能瓶颈

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
这一列表示Mysql决定如何查找表中的行,查找数据行记录的大概范围,依次从优到差分别是

	system>const>eq_ref>ref>range>index>ALL
	index-扫描全索引就能拿到结果,一般是扫描某个二级索引
	ALL-全表扫描,扫描你的聚集索引的所有叶子节点


## key_len
用到的索引长度,当用and关键字时,索引长度会随着条件而叠加
![image-20220312092841677](E:\Typora\Mysql\image-20220312092841677.png)

	char(n)-n字节长度
	varchar-如果是ntf-8,则长度是3n+2字节,加的2字节用来存储字符串长度,n是varchar自定义的长度
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
	当value为Using filesort时,将用外部排序,这种情况下需要考虑使用索引优化
	当value为Using index时,建立了索引

## 面试题：mysql在什么时候会选择使用全表索引

	在使用不等于（!=或者<>）的时候
	当范围查询的效率没有全表查询的效率高时
	is null,is not null
	like以通配符开头('$abc...')('%abc...')
	少用or或in,用它查询时,mysql不一定使用索引

## 索引总结图
假设index(a、b、c)

| Where语句                                        | 索引是否被使用                      |
| ------------------------------------------------ | ----------------------------------- |
| where a= 3                                       | Y,使用到a                           |
| where a=3 and b=5                                | Y,使用到a,b                         |
| where a= 3 and b=5 and c=4                       | Y, 使用到a,  b,  c                  |
| where b=3 或者 where b=3 and c=4  或者 where c=4 | N                                   |
| where a=3 and c=5                                | 使用到a,但是c不可以, b中间断了      |
| where a=3 and b>4 and c=5                        | 使用到a和b,c不能用在范围之后, b断了 |
| where a=3 and b like 'kk%' and c= 4              | Y,  使用到a, b, c                   |
| where a=3 and b like '%kk' and  c=4              | Y,  只用到a                         |
| wherea=3 and b like '%kk%' and c=4               | Y, 只用到a                          |
| wherea=3 and b like 'k%kk%' andc=4               | Y,使用到a,b,c                       |

like KK%相当于=常量,%KK和%KK%相当于范围




# 底层原理
Sql的底层是B+树

## 内部结构
	MySQL 大体可以分为Server层和存储引擎层两部分

![image-20220312144139527](E:\Typora\Mysql\image-20220312144139527.png)

## Server层
	包括连接器、查询缓存、分析器、优化器、执行器等,涵盖 MySQL 的大多数核心服务功能,以及所有的内置函数
## Store层
	存储引擎层负责数据的存储和提取
## 连接器
连接到这个数据库上,这时候接待你的就是连接器。连接器负责跟客户端建立连接、获取权限、维持和管理连接
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
	开始执行的时候,要先判断一下你对这个表 T 有没有执行查询的权限,如果没有,就会返回没有权限的错误。
# bin-log归档
	删库是不需要跑路的,因为我们的SQL执行时,会将sql语句的执行逻辑记录在我们的bin-log当中
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

### 面试题：为什么范围查找没有用索引下推,而like却能够用到
	Mysql认为范围查找过滤的结果集更大,like KK%在绝大数情况来看,过滤后的结果集较小。                

## Mysql优化器
```mysql
#开启trace,用完要关闭,不然浪费性能
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

和Case 3中explain的执行结果一样,但是出现了Using filesort,因为索引的创建顺序为 name,age,position,但是排序的时候age和position颠倒位置了。



Case5：

![image-20220313120740457](E:\Typora\Mysql\image-20220313120740457.png)



Case6：

![image-20220313120922852](E:\Typora\Mysql\image-20220313120922852.png)

	虽然排序的字段列与索引顺序一样,且order by默认升序,这里position desc变成了降序,导致与索引的排序方式不同,从而产生Using filesort

Case7:

![image-20220313121132041](E:\Typora\Mysql\image-20220313121132041.png)

	对于排序来说,多个相等条件也是范围查询,故产生了Using filesort

Case8：

![image-20220313121312683](E:\Typora\Mysql\image-20220313121312683.png)

	当使用*进行匹配时,此时并没有索引,故可以使用覆盖索引进行优化

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


	联合索引尽量覆盖条件,设计一个或者两三个联合索引(尽量少建单值索引),让每一个联合索引都尽量去包含sql语句里的where、order by、group by的字段,还要确保这些联合索引的字段顺序尽量满足sql查询的最左前缀原则。


	不要在小基数字段上建立索引,索引基数是指这个字段在表里总共有多少个不同的值,比如一张表总共100万行记录,其中有性别字段,其值不是男就是女,那么该字段的基数就是2
	一般建立索引,尽量使用那些基数比较大的字段,就是值比较多的字段,那么才能发挥出B+树快速二分查找的优势来。
	长字符串我们可以采用前缀索引,尽量对字段类型较小的列设计索引,比如说什么tinyint之类的,因为字段类型较小的话,占用磁盘空间也会比较小。


	对于这种varchar(255)的大字段可能会比较占用磁盘空间,可以稍微优化下,比如针对这个字段的前20个字符建立索引,就是说,对这个字段里的每个值的前20个字符放在索引树里,类似于 KEY index(name(20),age,position)。
	此时你在where条件里搜索的时候,如果是根据name字段来搜索,那么此时就会先到索引树里根据name字段的前20个字符去搜索,定位到之后前20个字符的前缀匹配的部分数据之后,再回到聚簇索引提取出来完整的name字段值进行比对。
	但是假如你要是order by name,那么此时你的name因为在索引树里仅仅包含了前20个字符,所以这个排序是没法用上索引的,group by也是同理。所以这里大家要对前缀索引有一个了解。


	where与order by冲突时优先where,一般这种时候往往都是让where条件去使用索引来快速筛选出来一部分指定的数据,接着再进行排序。因为大多数情况基于索引进行where筛选往往可以最快速度筛选出你要的少部分数据,然后做排序的成本可能会小很多。

## 分页查询优化
当数据非常大的情况下,如何优化

### 根据主键排序的
```mysql
# 优化前
select * from employees limit 90000,5;
# 优化后,改写后的SQL走了索引,条件:需要主键连续,主键不能够被删除或增加
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
	关联字段加索引,让mysql做join操作时尽量选择NLJ算法
	小表驱动大表,如果明确知道那张表是小表,可以用straight_join固定连接驱动方式
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
当B表的数据集小于A表时,in优先于exists
```mysql
select * from A where id in (select id from B)

#等价于
for(select id  from B ){
	select * from A where A.id = B.id
	}
```

## Exists
当B表的数据集大于A表时,exists优先于in
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
#临时关闭mysql查询优化,为了查看sql多次执行的真实时间
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
	插入或删除表数据行的时候同时维护计数表,让他们在同一个事务里操作


# 锁机制
将复杂的的多表连接交给java,单表交给mysql,它的空间很宝贵

## 分类
### 性能
	乐观锁用版本号去比对来实现锁,性能较好
```mysql
select * from t1 where version =1; 
```
	悲观锁,性能较差
### 操作类型
	读锁 和 写锁 都属于悲观锁
	读锁（共享锁,S（Shared））:针对同一份数据,多个读操作可以同时进行而不会互相影响
	写锁（排他锁,X（eXcusive））:当前写操作没有完成前,它会阻断其他写锁和读锁
### 操作粒度
#### 行锁
	开销大,加锁慢,会出现死锁;锁定粒度大,并发度高,锁冲突的概率最低,并发度最高。
	读锁会阻塞写,但是不会阻塞读。而写锁则会把读和写都阻塞。


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
	在Session_1下面执行 update account set name = "zhuge" where id > 8 and id < 18;则其他Session 没法在这个范围包含的所有行记录以及行记录所在的间隙里插入或删除任何数据,间隙锁是在可重复读隔离级别下才能生效。

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





