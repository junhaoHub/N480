# 计算机壳层（终极Shell——ZSH）
	Shell是Linux/Unix的一个外壳。它负责外界与Linux内核的交互，接收用户或其他应用程序的命令，然后把这些命令转化成内核能理解的语言，传给内核，内核是真正干活的，干完之后再把结果返回用户或应用程序。
## Zsh 是什么
	Zsh 属于 Shell 的一种，和 Bash 一样，但比 Bash 更好用，Zsh 完全兼容 Bash，拥有极其丰富的插件，其强大的自动补全参数、文件名以及自定义功能，可以大大提高我们使用 Linux 的效率。
## 详细介绍啊
https://zhuanlan.zhihu.com/p/19556676
## 查看系统是否安装ZSH
	cat /etc/shells

## 显示如下：
	/bin/bash
	/bin/csh
	/bin/ksh
	/bin/sh
	/bin/tcsh
	/bin/zsh

## 安装zsh
	Redhat Linux，执行：sudo yum install zsh
	Ubuntu Linux，执行：sudo apt-get install zsh
	安装完成后设置当前用户使用 zsh：chsh -s /bin/zsh

## 安装zsh主题
	第一次安装需要下载开发者工具，以便使用git下载，新版mac上删除了Xcode工具
	https://developer.apple.com/download/all/?q=Xcode%20Command%20Line%20Tools
	接着使用git下载zsh主题
	git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
	cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
	重新打开终端

## Mac系统的环境变量，加载顺序
	/etc/profile
	/etc/paths
	~/.bash_profile
	~/.zshrc

## 通用命令
	管道（PIPING）| 一种管道，其左方是一个命令的 STNOUT，将作为管道右方的另一个命令的 STDIN
	例如：echo ‘test text’ | wc -l
	注：wc命令用于计算字数。利用wc指令我们可以计算文件的Byte数、字数、或是列数，若不指定文件名称、或是所给予的文件名为"-"，则wc指令会从标准输入设备读取数据。


	>大于号，作用是取一个命令 STDOUT 位于左方，并将其写入/覆写（overwrite）入右方的一个新文件
	例如：ls > tmp.txt
	>>两个大于号，作用是取一个命令 STDOUT 位于左方，并将其追加到右方的一个新的或现有文件中。
	例如：date >> tmp.txt


	通配符（WILDCARDS）
	这类似于 SQL 中的 % 符号，例如，使用「WHERE first_name LIKE John% 」搜索所有以 John 起始的名字
	在 bash 中，相应的命令是「John*」。如果想列出一个文件夹中所有以「.json」结尾的文件，可以输入：「ls *.json」


退出命令：

Bash

	CTRL+C
	q
	exit


Python

	quit()
	CTRL+d

Nano

	CTRL+X



## 常用命令
	vim or nano：命令行编辑器
	touch {file}：创建一个新的空文件
	cp -R {original_name} {new_name}：复制一个文件或目录（包含内部所有文件）
	mv {original_name} {new_name}：移动或重命名文件
	rm -rf {file/folder}：永久删除文件或文件夹（小心使用）
	pwb：打印当前工作目录
	cat or less or tail or head -n10 {file}：文件的标准输出内容
	mkdir {directory}：创建一个空的目录
	grep -r {string} . ：在当前目录或子目录的文件中搜索一个字符串
	<mount/cat> column -t <delimited_file>：在 columnar格式中展示逗号分隔文件
	ssh {username}@{hostname}：连接到远程机器中
	top：任务管理器
	pip install --user {pip_package}：
		Python 安装包管理器，安装包到~/.local/bin 目录下

## 替换命令
	alias cls='clear'
	alias la='ls -a'
	alias dir='ls -l'
	alias type='cat'
	alias javac="javac -J-Dfile.encoding=utf8"
	alias grep="grep --color=auto"
	alias -s html=mate   #在输入后缀为 html 的文件名，会在 TextMate 中打开
	alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
	alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开
	alias -s js=vi
	alias -s c=vi
	alias -s java=vi
	alias -s txt=vi
	alias -s gz='tar -xzvf'
	alias -s tgz='tar -xzvf'
	alias -s zip='unzip'
	alias -s bz2='tar -xjvf'

# 简述编程语言
	编程语言(programming language),是用来定义计算机程序的形式语言.
	它是一种被标准化的交流技巧,用来向计算机发出指令,计算机收到指令并执行相应的动作
##	硬件
### CPU频率
简单说是CPU运算时的工作频率(1秒内发生的同步脉冲数.单位是Hz)
	1khz=1024hz
	1mhz=1024khz
	1ghz=1024mhz

###	存储单位
计算机中最基本的储存单位是字节(byte).

	一个0(关)或者一个1(开)储存为一个bit,每个字节由8个比特(bit)组成

### 屏幕像素
	长的像素点*宽的像素点
	像素密度(ppi)=V[[长度像素数]^2+[宽度像素数]^2]/屏幕尺寸

##	进化史
编程产物

	算法+数据结构=程序(例如：数组,线性结构,树形结构,二叉树)
	Algorithms+Data Structures=Programs

初代语言

	机器语言：使用01关开(也用于表示boolean值,false,true)

二代语言

	汇编语言：使用助记语言(例如add)

三代语言

	高级语言：C(面向过程) Java  Python scala C#(面向对象) 

##	编程语言分类

Java

	Java作为构建现代企业Web应用后端的最常用编程语言之一,是一门必须掌握的编程语言.
	网站开发人员凭借Java和基于Java的框架可以为各种用户创建可扩展性的Web应用.
	Java也是用来开发Android系统原生应用的主要编程语言之一.

JavaScript

	现代网站离不开JavaScript.
	如果你想为你的网站创造互动,或者用时下流行的JavaScript框架搭建一个用户界面,那么JavaScript是最佳选择.

C#

	C#是Windows开发环境下的主要语言.
	无论你是用微软云计算平台Windows Azure和.NET框架来创建现代网页应用,
	还是开发Windows设备上的应用,又或者是为你的企业开发功能强大的桌面应用,
	C#能够迅速地帮助你驾驭微软提供的所有功能.
	可以开发游戏吗？当然,非常流行的Unity游戏开发引擎就把C#作为了自己的主要开发语言

Python

    Python几乎可以做任何编程工作.
    Web应用,用户交互界面,数据分析,统计等等.
    不管是什么问题,你总能在Python中找到需要的框架.
    近期,Python又成为了数据科学家用来筛选大型数据集的重要工具.

SQL

    数据很重要,它无处不在,它复杂多变.
    这时候,你需要SQL来帮助你以快速,可重复且可靠的方式准确找到信息.
    有了SQL,你可以轻轻松松地从庞大复杂的数据库中查询,提取有意义的数据.

R语言

    R语言推动了大数据革命,也是数据分析研究者在2015年不可不知的编程语言之一.
    从科学和商业到娱乐和社会媒体,每一个需要统计分析的领域都少不了R语言.

Scala

    Scala编程语言抓住了很多开发者的眼球.
    如果你粗略浏览Scala的网站,你会觉得Scala是一种纯粹的面向对象编程语言,
    而又无缝地结合了命令式编程和函数式编程风格.

C

	广泛应用于底层开发，以简易的方式编译、处理低级存储器，不需要任何运行环境支持便能运行的高效率程序设计语言

C++

	保持了对C语言的兼容性，允许指针的存在，允许程序员手动高效的管理内存，凭借先进的数值计算库、泛型编程等优势在游戏领域应用颇多
	可以开发游戏吗？当然,非常流行的Unreal游戏开发引擎就把C++作为了自己的主要开发语言

Go

	Go语言更适合开发大型软件，并且开发周期较长，支持云计算的网络服务，也就是服务器端软件，Go语言能够让程序员快速开发，并且在软件不断的增长过程中，它能让程序员更容易地进行维护和修改。它融合了传统编译型语言的高效性和脚本语言的易用性和富于表达性。

# 主学Java的特点
面向对象
	面向对象的两个基本概念：类、对象(类派生出来的)

健壮性
	提供了一个相对安全的内存管理(垃圾回收机制)和访问机制
	去除了c语言的指针、自动的回收机制 ——》仍然会出现内存溢出、内存泄露

跨平台性
	安装一个JVM(Java Virtual Machine)即可实现write once,run anywhere

## 面向对象的特性
    封装、多态、继承、抽象

## 特性和特点的区别
    特点一般指外在的，特性指的是内在的

## JDK、JRE、JVM

	JDK包括JRE，JRE包括JVM，所以只需要在不同系统中
	选择不同版本的JDK，就可以实现运行。


	JDK可以支持Java程序的开发,包括编译器(javac.exe)开发工具(javadoc.exe、jar.exe、keytool.exe、jconsole.exe)和更多的类库(如tools.jar)等.
	JRE可以支撑Java程序的运行,包括JVM虚拟机(java.exe等)和基本的类库(rt.jar).   

# 关键字
被Java语言赋予了特殊含义，用做专门用途的字符串

## 特点
关键字中所有的字母均为小写
    
## 标识符
Java对各种变量、方法和类等要素命名时使用的单词称为标识符
	
	助记：是自己可以起名字的都是标识符
	命名规则：
	由26个英文字母大小写，0-9，_或$组成
		①数字不可以开头
		②不可以使用关键字和保留字
		③标识符不能包含空格
	保留字：
		goto、const

# 命名规范
类名\接口1024

	XxxxYyyy


## 方法名

	变量名\参数名\成员变量\局部变量(函数名)(实例化对象名)
	xxxYyyZzz
	int userId = a++
	void myClass 


## 常量名 

	所有字母都大写.多单词时每个单词用下划线连接
	int NAME_ID =3

## 抽象类
	命名使用 Abstract 或 Base 开头

## 异常类
	命名使用 Exception 结尾

## 测试类
	命名以它要测试的类的名称开始,以 Test 结尾

## 布尔类型
	变量不要用is开头

## 自定义类型
	在自定义类里面写上简单易懂的单词

## 标识符
	我们对各种 接口\变量\方法\类 等要素命名时使用的字符序列(单词)称为标识符

# 基本数据类型变量
变量都定义在作用域内(“{}”),在作用域内("{}"),它是有效的
## 整型

	.byte (1字节) .short(2字节) int(4字节) long(8字节)
	1byte=8bit  字节(byte)范围：-128 ~ 127    2^8=256      

## 浮点型
	float(4字节)/double(8字节)
	float精确到尾数后七位,声明浮点型变量,必须以f结尾

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
float f1 = 1324.2333f
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


## 字符型变量
	char(2字节) 
	定义char型变量,通常使用一对' ' , 内部只能写一个字符
	在使用加法运算时,会自动转化为int类型
	char a = 'c'

## 长整型变量
	long(4字节)
	声明long型变量,必须以L结尾
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
long l2 = 7882333882L
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## 布尔型变量
Java虚拟机直接支持 boolean类型的数组
虚拟机中没有供 boolean值专用的字节码指令,Java语言表达式所操作的boolean值,在编译之后都使用虚拟机中的int数据类型来代替。
因为在虚拟机规范中说了，boolean值在编译之后都使用Java虚拟机中的int数据类型来代替，而int是4个字节，那么boolean值就是4个字节。
boolean类型数组的访问与修改共用byte类型数组的baload和 bastore指令，因为两者共用，只有两者字节一样才能通用呀，所以byte数组中一个byte是1个字节，那么boolean数组中boolean是1个字节。

	Java规范中，没有明确指出boolean的大小。在《Java虚拟机规范》给出了单个boolean占4个字节，和boolean数组1个字节的定义，具体 还要看虚拟机实现是否按照规范来，所以1个字节、4个字节都是有可能的

# 注释

用于有关变量,方法,类或其他任何语句的解释,并且不被编译器和解释器执行的语句.

##  注释的作用
更加方便的记录你的程序信息,更容易理解代码属性及方法.

## 如何使用
    java有三种注释：
    .文档注释 
    /**
    @auther fujunhao
    @version v1.0
    这是我的第一个java程序!非常的开心
    */

只有 /**    */ 才可以被javadoc解析
            .单行注释   
            //This is single line comment
            .多行注释  
            /* This is multi line comment */
##  斜杠与反斜杠
###  斜杠
    / 表示地址路径的下一级目录 
### 反斜杠
	\ 表示转义字符
	如果要输出反斜杠“\”也需要用转义字符：“\\” ,例如:\\n:能够输出\n.
## 转移符号
	\n:表示换行
	\":表示一个英文半角的双引号
	\t:表示一个tab键的空格
	\b:表示一个退格键,放到字符串的两边没有效果
	\r\n:windows操作系统不认识\n,只认识\r\n
	\\:表示一个\(适合输出到Windows系统)
	@符号
	1,取消\在字符串中的转义作用
	2,将字符串按照原格式输出

# 类型转换
	byte,short,char -->int -->long-->float-->double
	当byte\char\short进行混合运算时,会自动提升为int

## 强制转换

``` java
float l1 = 12.9;
int a = (int)l1;
```

将String类型转化为int类型

``` java
int num1 = Interger.parseInt(str1);
System.out.println(num1);
```

指定转换到小数点后几位
```java
system.out.println("{0:0.00}",d);
//表示留变量d的小数点后2位
```

# 运算符
## 取模(取余) % 
% 长得像一条鱼,所以是取余.结果的符号与被模数的符号相同,与模数无关.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
7%5=2
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## 除号(取整) /
/ 长得像把刀,所以是斩断小数,只留商.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
7/5=1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## 加号
    ++a :先自增1,在将加数赋给被加数.
    a++：先将加数值赋给被加数,在自加一

## 比较
    比较运算符:== !=  >= <=
    赋值运算符:=  
    扩展赋值运算符：+=,-=,*=,/=,%=

## 逻辑
区分&与&& 和 |与||
### 且
	当符号左边是false时,&会继续执行符号右边的计算,&&不再执行右边的计算
### 或
	当符号左边是false时,|会继续执行符号右边的计算,||还会再执行右边的计算

## 位运算符
	<<:在一定范围内,每向左移一位,相当于 * 2
	>>:在一定范围内,每向右移一位,相当于 / 2  


## 三元运算符
(条件表达式)?表达式1:表达式2

	条件表达式的结果为boolean类型
	如果表达式为true,则表达式为1
	如果表达式为false,则表达式为2 

# 原码
计算机底层都以补码的方式来存储数据

## 正数
	正数的原码、反码、补码都相同

## 负数
	-14的原码：
		10001110
		反码：
		11110001
		补码：
		11110010

## 补码的用处
	计算机中表示负数，如果用最高位表示符号这种“原码”方式，虽然有利于人的阅读，但不利于其本身的计算。所以系统内部就把负数统一用“其对应正数的补码”来表示，而正数自己不用改变。

# 判断语句
## 循环结构
for与while的区别

    For是在已知终止条件的情况下进行循环
    while是在未知终止条件的情况下进行循环
while与do while的区别

	while 是先判断,后执行
	do while是先执行,后判断

## 循环条件
### break	
使流程跳出switch语句体

	也可以用break语句在循环结构终止本层循环体,从而提前结束本层循环    
	只能在循环体和switch语句体内使用break

### continue
跳过本次循环体中本次尚未执行的语句,搭配Label使用，理解为仅结束本次循环.

	continue语句并没有使整个循环终止
	continue label:结束label后面的当次循环

# 数组
多个类型相同的元素按照顺序排列，对数组进行命名，通过角标的方式对这些数据来进行统一管理

## 特点
	有序排列，引用类型变量，数组的元素既可以是基本数据类型，又可以是引用数据类型
	因为数组在内存中是一块连续的空间，所以长度一旦确定，不能更改

## 一维数组
```java
int num; //声明
num = 10; //初始化
int id = 1001; //声明+初始化
```

## 实例化指定位置的元素
### 元素未知
```java
//可以进行动态初始化变量
String [] args = new String[5];
```

### 元素已知
```java
//可以进行静态初始化变量
int [] args1 = new int[]{1001,1002,1003,1004,1005};
args[0] = "马云";//实例化数组的第一位元素为String类型
```
### 获取数组的长度
```java
System.out.println(args.length);//依靠属性length
```

### 数组元素的初始化值
	boolean : 0代表false
	char : ascii码值 0或\u0000,而非字符'0'
	String : null	

## 二维数组
数组的元素又是一串数组
```java
//静态初始化
int[][] arr1 = new int[][] {{11,12,13},{14,15,16}};
//动态初始化
String [][]arr2 = new  String[3][5];
```
### 指定位置的元素
```java
System.out.println(arr4[0][0]);
```

### 查询数组长度
```java
System.out.println(arr4.length);
```

### 遍历数组
```java
//arr[i],它代表着外层的第几个数组
for (int i = 0; i < arr.length; i++) {
   for (int j = 0; j < arr[i].length; j++) {
    System.out.print("["+arr[i][j]+"]"+",");
   }
   System.out.println();
  }
```
### 二维数组元素的默认初始化值
在指定外层数组,但没指定内层数组的情况下 或 引用数据类型默认值 显示为null
```java
System.out.println(arr4);
//arr4数组的地址值
System.out.println(arr4[1]);
//arr4[1]数组的地址值
```
在指定内层和外层数组的情况下显示为数组的元素
```java
System.out.println(arr4[1][0]);//数据
```
### 内存解析

![image-20220218114916413](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220218114916413.png)
