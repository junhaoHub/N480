#   概述
## 体系结构
### 面向应用的API
	JAVA API抽象接口,供应用程序开发人员使用（连接数据库，执行SQL语句，获得结果）
	JDBC是sun公司提供一套用于数据库操作的接口,java程序员只需要面向这套接口编程即可
### 面向数据库的API
	Java Driver API,供开发商开发数据库驱动程序用。
	不同的数据库厂商,需要针对这套接口,提供不同的实现不同的集合,即为不同数据库的驱动.

## 步骤图

![image-20220228215418215](E:\Typora\JDBC\image-20220228215418215.png)

## ODBC
	(**Open Database Connectivity**,开放式数据库连接),只需要调用ODBC API,由 ODBC 驱动程序将调用转换成为对特定的数据库的调用请求

## 前端技术栈

![image-20220228215658806](E:\Typora\JDBC\image-20220228215658806.png)


# 获取数据库连接
## Driver接口实现类
	程序中不需要直接去访问实现了Driver 接口的类,而是由驱动程序管理器类(java.sql.DriverManager)去调用这些Driver实现


	Oracle的驱动：oracle.jdbc.driver.OracleDriver
	mySql的驱动：com.mysql.cj.jdbc.Driver
![image-20220228220523449](E:\Typora\JDBC\image-20220228220523449.png)

	将上述jar包拷贝到Java工程的一个目录中,习惯上新建一个lib文件夹
	在驱动jar上右键-->Build Path-->Add to Build Path


	如果是Dynamic Web Project（动态的web项目）话,则是把驱动jar放到WebContent（有的开发工具叫WebRoot）目录中的WEB-INF目录中的lib目录下即可

##  URL
	URL 用于标识一个被注册的驱动程序,驱动程序管理器通过这个 URL 选择正确的驱动程序,从而建立到数据库的连接。
	URL的标准由三部分组成,各部分间用冒号分隔


	jdbc:子协议:子名称


	协议:JDBC URL中的协议总是jdbc 
	子协议:子协议用于标识一个数据库驱动程序
	子名称:一种标识数据库的方法,定位数据库,包含 //主机名(对应服务端的ip地址)/端口号/数据库名

## 代码示例
```java
url=jdbc:mysql://localhost:3306/test?rewriteBatchedStatements=true
```


## 常用数据库的  URL
### MySQL
	jdbc:mysql://主机名称:mysql服务端口号/数据库名称?参数=值&参数=值
	jdbc:mysql://localhost:3306/test
	jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf8

### Oracle 9i
	jdbc:oracle:thin:@主机名称:oracle服务端口号:数据库名称
	jdbc:oracle:thin:@localhost:1521:test

### SQLServer
	jdbc:sqlserver://主机名称:sqlserver服务端口号:DatabaseName=数据库名称
	jdbc:sqlserver://localhost:1433:DatabaseName=test


## 代码示例
```java
	@Test
    public void testConnection4() {
            //1.配置文件的4个基本要素：
            url="jdbc:mysql://localhost:3306/test"
            user="root"
            password="123456"
            driverClass="com.mysql.cj.jdbc.Driver"

            //2.加载配置文件
            InputStream is = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties")
            Prrperties pros = new Properties();
            pros.load(is)
            //3.加载驱动 （①实例化Driver ②注册驱动）
            Class.forName(driverClass);

            //4.注册驱动
            //DriverManager.registerDriver(driver);
            /*
            可以注释掉上述代码的原因，是因为在mysql的Driver类中声明有：
            static {
                try {
                    DriverManager.registerDriver(new Driver());
                } catch (SQLException var1) {
                    throw new RuntimeException("Can't register driver!");
                }
            }
     				*/
          
            //3.获取连接
            Connection conn = DriverManager.getConnection(url, user, password);
            System.out.println(conn);
        } 

    }
```

# PreparedStatement

## 操作和访问数据库
数据库连接被用于向数据库服务器发送命令和 SQL 语句，并接受数据库服务器返回的结果。

## 对数据库调用的不同方式
### Statement
	用于执行静态 SQL 语句并返回它所生成结果的对象。 
### PrepatedStatement
	SQL 语句被预编译并存储在此对象中，可以使用此对象多次高效地执行该语句。
### CallableStatement
	用于执行 SQL 存储过程

## 面试题：为什么使用Preparedstatement
### 优点
	允许动态和参数化的query(易于维护)
	预编译，Statement基本上无法重用。所以多次执行的话前者效率高很多。 
	预防sql注入的攻击
	Preparedstatement 操作Blob的数据，而Statement做不到
	PreparedStatement 可以实现更高效的批量操作

### Statement示例代码
```java
Statement stmt = conn.createStatement("INSERT INTO students VALUES('" + user + "')");
stmt.execute();
```

If "user" came from user input and the user input was

```java
Robert'); DROP TABLE students; --
```

	如果statement 会把 table drop掉。而prepared的呢因为已经预编译了,根本就不会执行这个drop语句。

```mysql
//通过Mysql注入查询表中所有数据
SELECT user,password FROM user_table WHERE USER = '1' or ' AND PASSWORD = '='1' or '1' = '1';
```


![image-20220228222748787](E:\Typora\JDBC\image-20220228222748787.png)

### Java与SQL对应数据类型转换表
数据库表中的字段名可以和属性名不同，但类型需要相同
| Java类型           | SQL类型                  |
| ------------------ | ------------------------ |
| boolean            | BIT                      |
| byte               | TINYINT                  |
| short              | SMALLINT                 |
| int                | INTEGER                  |
| long               | BIGINT                   |
| String             | CHAR,VARCHAR,LONGVARCHAR |
| byte   array       | BINARY  ,    VAR BINARY  |
| java.sql.Date      | DATE                     |
| java.sql.Time      | TIME                     |
| java.sql.Timestamp | TIMESTAMP                |

### 查询操作的流程

![image-20220228222807559](E:\Typora\JDBC\image-20220228222807559.png)

# 数据库事务
一个或多个DML操作，使数据从一种状态变换到另一种状态

## 目的
	为数据库操作提供了一个从失败中恢复到正常状态的方法,同时提供了数据库即使在异常状态下仍能保持一致性的方法

## 特性
ACID，原子性，一致性，隔离性，持久性
### 原子性
	Atomicity,事务包含的操作全部成功或者全部失败

### 一致性
	Consistency,数据库从一个一致性状态变到另一个一致性状态
	由于系统异常或数据库系统出现故障导致只有部分数据更新成功,但是这不是我们需要的最终数据,这就是数据的不一致

### 隔离性
	Isolation,当多个应用程序在并发访问数据库时,可以在这些应用程序之间提供一个隔离方法,防止彼此的操作互相干扰

### 持久性
	Durability,事务提交后数据应该被永久的保存下来，出现宕机等故障后可以恢复数据

## 回滚
	rollback,当出现了系统故障,为了保证所有事务都作为一个工作单元来执行,数据库管理系统将放弃所做的所有修改,整个事务回滚到最初状态。

### 规则
	数据一旦提交,就不可回滚
	DDL操作一旦执行,就会自动提交
	DML操作情况下,一旦执行，就会自动提交
	默认在关闭连接时,会自动地提交数据
	可以通过set autocommit = false的方式取消DML操作的自动提交,但对DDL失效

## 隔离级别
数据库事务的隔离级别有4种，在事务的并发操作中可能会出现脏读，不可重复读，幻读。

### 脏读
	当一个事务正在访问数据,并且对数据进行了修改，而这种数据还没有提交到数据库中,这时,另外一个事务也访问这个数据，然后使用了这个数据。因为这个数据还没有提交那么另外一个事务读取到的这个数据我们称之为脏数据。
### 不可重复读
	对于两个事务T1,T2。T1从一个表中读取了一个字段，然后T2(更新)了该字段。之后,T1再次读取到同一个字段，值还是T1
### 幻象读
	两个事务T1,T2 T1从一个表中读取了一个字段,然后T2在表中(插入)一些新的行。如果T1再次读取同一个表,就会多出几行

| 隔离级别                                        | 描述                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| READ UNCOMMITED （读未提交数据）                | 允许事务读取未被其他事务提交的变更，脏读，不可重复读和幻读的问题都会出现 |
| READ  COMMITED       （读已提交数据）           | ※只允许T1事务读取 T2事务已commit的变更，只可以避免脏读       |
| REPEATABLE     READ                （可重复读） | ※ 确保事务可以多次从一个字段中读取相同的值，在这个事务持续期间， 禁止其他事务对这个字段进行更新，可以避免脏读和不可重复读 |
| SERIALZABLE                   (串行化)          | 确保事务可以从一个表中读取相同的行，在这个事务持续期间，禁止其他事务对该表执行插入、更新和删除操作，所有的并发问题都可以解决，但性能十分低下 |
