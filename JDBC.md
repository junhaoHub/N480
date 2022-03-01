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

## 面试题：为什么使用Preparedment
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

### 3.3 Java与SQL对应数据类型转换表
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

### 3.4查询操作的流程

![image-20220228222807559](E:\Typora\JDBC\image-20220228222807559.png)



