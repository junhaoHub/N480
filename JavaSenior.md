# 多线程
独立运行的程序片段叫做线程，从软件或硬件上实现多个线程并发执行的技术叫做多线程

## 应用
### start()
	使该线程开始执行,并执行对象的run()方法
### run()
	线程在被调度时执行的操作
### String getName()
	返回线程的名称
### setName(String name) 
	改变线程名称,使之与参数 name 相同
### static Thread currentThread()
	返回当前线程,在Thread子类中就是this,通常用于主线程和Runnable实现类

## 代码示例

```java
public class RunnableTest {
    public static void main(String[] args) {
        MyThread2 myThread2 = new MyThread2();

        Thread thread = new Thread(myThread2);
        thread.setName("线程1");
        thread.start();

        Thread thread2 = new Thread(myThread2);
        thread2.setName("线程2");
        thread2.start();
    }
}

class MyThread2 implements Runnable{
    @Override
    public void run() {
        for (int i =0;i<100;i++){
            if (i%2==0){
                System.out.println(i);
            }
        }
    }
}
```

## 生命周期图例
![image-20220227094237259](E:\Typora\JavaSenior\image-20220227094237259.png)

## 并行
	多个CPU同时执行多个任务
## 并发
	一个CPU（采用时间片）同时执行多个任务。比如：秒杀、多个人做同一件事情	

# 线程安全
共同拥有共享数据的多条线程并行执行的程序中，会通过同步机制保证各个线程都可以正常

## 信息不同步
	当同时有多个线程运行相关联的程序，第一个程序正在执行判断条件，在这时,其他线程也去进行逻辑判断，并且执行，接着线程一判断为true之后,也进行重复执行

## 同步监视器
	俗称锁.任何一个类都可以充当锁
### 规则
	多个线程必须共用同一把锁
### 优点
	使用同步的方式,解决了线程的安全问题.
### 缺点	
	操作同步代码时,只能有一个线程参与,相当于是一个单线程的过程,效率低下

## 实际场景
	当某个线程操作车票的过程中,尚未操作完成时,其他线程参与进来,也操作车票.
### 解决方法
	加锁,当一个线程a在操作ticket的时候,其他线程不能参与进来.直到线程a操作完ticket时,其他线程才可以开始操作ticket.这种情况即使线程a出现了阻塞,也不能改变
	我们通过同步机制,来解决线程的安全问题,在实现Runnable接口创建多线程的方式中,我们可以考虑使用this充当同步监视器

#### 同步代码块
	synchronized(同步监视器){
		//需要被同步的代码
	}

#### 同步方法

	如果操作共享数据的代码完整的声明在一个方法中,我们不妨将此方法声明为同步的
	同步方法中的同步监视器:非静态的同步方法,同步监视器是:this静态的同步方法,是类本身 
	操作共享数据的代码,即为需要被同步的代码,不能包含代码多了,也不能包含代码少了

##### 代码示例
```java
public class WindowsTest {
    public static void main(String[] args) {//存在线程安全问题
        Window w1 = new Window();

        Thread t1 = new Thread(w1);
        Thread t2 = new Thread(w1);
        Thread t3 = new Thread(w1);
        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();

    }
}
class Window implements Runnable{
    private  int ticket = 100;
    Object obj = new Object();


    @Override
    public void run() {
        while (true){
            synchronized(obj){
                if (ticket>0){
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName()+"卖票,票号为："+ticket);

                    ticket--;


                }else {
                    break;
                }
            }
        }
    }
}
```

#### Lock锁

	JVM将花费更少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）

##### 代码示例
```java
public class LockTest {
    public static void main(String[] args) {
        Window3 window3 = new Window3();

        Thread thread = new Thread(window3);
        Thread thread1= new Thread(window3);
        thread.start();
        thread1.start();
    }
}

class Window3 implements Runnable{

    private int ticket =100;
    private ReentrantLock lock = new ReentrantLock(true);

    @Override
    public void run() {
        while (true) {

               try {
                   lock.lock();
                   if (ticket > 0) {
                       try {
                           Thread.sleep(100);
                       } catch (InterruptedException e) {
                           e.printStackTrace();
                       }
                       System.out.println(Thread.currentThread().getName() + "售票为" + ticket);
                       ticket--;
                   } else {
                       break;
                   }
               }finally {
                   lock.unlock();
               }


            }
    }
}
```

## 优先使用顺序:

Lock→同步代码块(已经进入了方法体,分配了相应资源)→ 同步方法(在方法体之外)

## 面试题: synchonize 与 Lock锁的异同点
### 相同点
	二者都可以解决线程安全问题
### 不同点
	synchronized机制在执行完相应的同步代码以后,自动的释放同步监视器
	Lock需要手动的启动同步(lock()),同时结束同步也需要手动的实现(unlock())

## 面试题:如何解决线程安全问题?有几种方法
### wait()
	一旦执行此方法,当前线程就进入阻塞状态,并释放同步监视器
### notify()
	一旦执行此方法,就会唤醒被wait的一个线程.如果有多个线程被wait,就唤醒优先级高的
### notifyAll()
	一旦执行此方法,就会唤醒所有被wait的线程

### 规则
	这三个方法必须使用在同步代码块或同步方法中 
	这三个方法的调用者必须是同步代码块或同步方法中的同步监视器

## 面试题:死锁
不同的线程分别占用对象需要的同步资源不放手,都在等待对方放弃自己需要的同步资源

### 代码示例
```java
public class DeadLockTest {
    public static void main(String[] args) {

        StringBuffer s1 = new StringBuffer();
        StringBuffer s2 = new StringBuffer();

        new Thread(){
            @Override
            public void run() {
                synchronized (s1){
                    s1.append("a");
                    s2.append("1");
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    synchronized (s2){
                        s1.append("b");
                        s2.append("2");
                    }
                }
            }
        }.start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (s2){
                    s2.append("c");
                    s1.append("3");
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (s1){
                        s1.append("d");
                        s2.append("4");

                        System.out.println(s1);
                        System.out.println(s2);
                    }
                }
            }
        }).start();
    }
}
```
### 症状
	出现死锁后,不会出现异常,不会出现提示,只有所有的线程都处于阻塞状态,无法继续
### 解决方法
	专门的算法,原则
	尽量减少同步资源的定义
	尽量避免嵌套同步

## 面试题:sleep()和wait()的异同
### 相同点
	一旦执行方法,都可以使得当前的线程进入阻塞状态
### 不同点
#### 两个方法声明的位置不同
	Thread类中声明的sleep()
	object类中声明wait()
#### 调用的要求不同
	sleep()可以在任何需要的场景下调用
	wait()必须使用在同步方法或同步代码块中
#### 是否释放同步监视器
	如果两个方法都使用在同步代码块或同步方法中
	sleep()不会释放锁
	wait()会释放锁

## 面试题:如何理解Callable接口的方式创建多线程比实现Runnable接口创建多线程方式强大
	call()可以有返回值的
	call()可以抛出异常,被外面的操作捕获,获取异常的信息
	Callable是支持泛型的    

## 面试题:线程池的好处
	提高响应速度(减少了创建新线程的时间)
	降低资源消耗(重复利用线程池中线程,不需要每次都创建)	
	便于线程管理       

# 常用类
## String
字符串，所有java中的字符串字面值都作为此类的实例实现
### 特性
	String是一个final类,代表不可变的字符序列,不可被继承
	字符串是常量,他们的值在创建后不能更改
	String对象的字符内容是存储在一个字符数组value[]中
	实现了Comparable接口，可以比较大小
	不会存储相同内容的字符串
### 示例代码
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence,
               Constable, ConstantDesc {
    @Stable
    private final byte[] value;
```

### 内存图

![image-20220228095413233](E:\Typora\JavaSenior\image-20220228095413233.png)

### 示例代码
```java
String s1 = "test";
String s2 = new String("test");
```
### 内存图

![image-20220228113215834](E:\Typora\JavaSenior\image-20220228113215834.png)

### 方法
#### charAt(int index) 
	返回指定索引处的 char 值
#### compareTo(String anotherString) 
	按字典顺序比较两个字符串
#### compareToIgnoreCase(String str) 
	按字典顺序比较两个字符串，不考虑大小写 
#### concat(String str) 
	将指定字符串连接到此字符串的结尾 
#### contains(CharSequence s) 
	当且仅当此字符串包含指定的 char 值序列时,返回 true
#### endsWith(String suffix) 
	测试此字符串是否以指定的后缀结束
#### equals(Object anObject) 
	将此字符串与指定的对象比较
#### equalsIgnoreCase(String anotherString) 
	将此 String 与另一个 String 比较，不考虑大小写 
#### hashCode() 
	返回此字符串的哈希码
#### indexOf(String str) 
	返回指定子字符串在此字符串中第一次出现处的索引
#### indexOf(String str, int fromIndex) 
	返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始 
#### isEmpty() 
	当且仅当 length() 为 0 时返回 true 
#### lastIndexOf(int ch) 
	返回指定字符在此字符串中最后一次出现处的索引
#### lastIndexOf(int ch, int fromIndex) 
	返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索 
#### length() 
	返回此字符串的长度
#### matches(String regex) 
	告知此字符串是否匹配给定的正则表达式 
#### replace(char oldChar, char newChar) 
	返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 
#### replace(CharSequence target, CharSequence replacement) 
	使用指定的字面值替换序列 替换 此字符串所有匹配字面值目标序列的子字符串 
#### replaceAll(String regex, String replacement) 
	使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串
#### replaceFirst(String regex, String replacement) 
	使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串 
#### String[] split(String regex) 
	根据给定正则表达式的匹配拆分此字符串 
#### String[] split(String regex, int limit) 
	根据匹配给定的正则表达式来拆分此字符串。 
#### startsWith(String prefix) 
	测试此字符串是否以指定的前缀开始。 
#### startsWith(String prefix, int toffset) 
	测试此字符串从指定索引开始的子字符串是否以指定前缀开始
#### substring(int beginIndex) 
	返回一个新的字符串，它是此字符串从beginIndex开始截取到最后一个子字符串
#### substring(int beginIndex, int endIndex) 
	返回一个新字符串，它是此字符串从begIndex截取到endIndex(不包含)的一个子字符串 
#### toLowerCase() 
	使用默认语言环境的规则将此 String 中的所有字符都转换为小写 
#### toUpperCase() 
	使用默认语言环境的规则将此 String 中的所有字符都转换为大写
#### trim() 
	返回字符串的副本,忽略前导空白和尾部空白

## StringBuilder
### 面试题：String、StringBuffer、StringBuilder异同
### String
	不可变的字符序列;底层使用char[]存储
### StringBuffer
	可变的字符序列;底层使用char[]存储;线程安全的，效率低
### StringBuilder
	可变的字符序列;底层使用char[]存储;线程不安全的，效率高;jdk5.0新增

### 扩容
	扩容为原来容量的2倍 + 2,同时将原有数组中的元素复制到新的数组中。
	开发中建议使用:StringBuffer 或 StringBuilder(int capacity)
### 应用
#### 增
	append(xxx)
#### 删
	delete(int start,int end)
#### 改
	setCharAt(int n ,char ch) 
	replace(int start, int end, String str)
#### 查
	charAt(int n)
#### 插
	insert(int offset, xxx)
#### 长度
	length()
#### 遍历
	for() + charAt() / toString()

## 日期类
### java.sql.Date
	对应着数据库中的日期类型的变量,java.sql.Date只是精确到某一天
### java.util.Date
	存入的数据精确到每天的具体的某个时间段

### 应用
#### toString()
	显示当前的年、月、日、时、分、秒（Tue Mar 01 15:18:06 CST 2022）

#### getTime()
	获取当前Date对象对应的毫秒数.（1646119086096）

#### 使用多态将 java.sql.Date 打印出年月日
```java
java.sql.Date date1 = new java.sql.Date(89123556768L);
System.out.println(date1);
```

### java.util.Date转换为java.sql.Date
```java
//情况一:
Date date4 = new java.sql.Date(1234123123L);
java.sql.Date date7 = (java.sql.Date)date4;
```
```java
//情况二:
Date date6 = new Date();
java.sql.Date date7 = new java.sql.Date(date6.getTime());
```
## 比较器
	集合存储的元素的特性,如果元素是可比较的则可以进行相应的排序,否则不行
### 优点
	Java中的对象,正常情况下,只能进行比较: == 或 != 。不能使用 > 或 < 的
	但是在开发场景中,我们需要对多个对象进行排序,言外之意,就需要比较对象的大小

## 应用
###  sort() 
	对指定的 类型 数组按字母升序进行排序。

###  自然排序
java.lang.Comparable
### 示例代码
```java
//指明商品比较大小的方式
@Override
public int compareTo(Object o) {
        if (o instanceof Goods) {
            Goods goods = (Goods) o;
            if (this.price > goods.price) {
                return 1;
            } else if (this.price < goods.price) {
                return -1;
            } else {
                return Double.compare(this.price, goods.price);
            }
        }
        return 0;
}
```


###  定制排序
java.util.Comparator

	当元素的类型没有实现 Comparable 接口而又不方便修改代码,那么可以考虑使用 Comparator 的对象来排序
### 示例代码
```java
@Test
public void test3(){
      String[] arr = new String[]{"AA","CC","KK","MM","GG","JJ","DD"};
      Arrays.sort(arr,new Comparator(){

            //按照字符串从大到小的顺序排列
            @Override
            public int compare(Object o1, Object o2) {
                if(o1 instanceof String && o2 instanceof  String){
                    String s1 = (String) o1;
                    String s2 = (String) o2;
                    return -s1.compareTo(s2);
                }
//                return 0;
                throw new RuntimeException("输入的数据类型不一致");
            }
        });
        System.out.println(Arrays.toString(arr));
    }

```

## 系统类

### 方法
#### currentTimeMillis() 
	返回以毫秒为单位的当前时间
#### exit(int status) 
	终止当前正在运行的 Java 虚拟机，非0的状态码表示异常终止
#### gc() 
	运行垃圾回收器
### getProperties(String key) 
	获取指定键指示的系统属性
### 代码示例
```java
@Test
public void test1() {
        String javaVersion = System.getProperty("java.version");
        System.out.println("java的version:" + javaVersion);

        String javaHome = System.getProperty("java.home");
        System.out.println("java的home:" + javaHome);

        String osName = System.getProperty("os.name");
        System.out.println("os的name:" + osName);

        String osVersion = System.getProperty("os.version");
        System.out.println("os的version:" + osVersion);

        String userName = System.getProperty("user.name");
        System.out.println("user的name:" + userName);

        String userHome = System.getProperty("user.home");
        System.out.println("user的home:" + userHome);

        String userDir = System.getProperty("user.dir");
        System.out.println("user的dir:" + userDir);

}
```

## 数学类
java.lang.Math提供了一系列静态方法用于科学计算。其方法的参数和返回值类型为double型
### abs
	绝对值
### acos,asin,atan,cos,sin,tan
	三角函数
### sqart
	平方根
### pow(double a,doble b)
	a的b次幂
### log
	自然对数
### exp
	e为底指数
### random()
	返回0.0到1.0的随机数
### long round(double a)
	double型数据a转换为long型（四舍五入）
### toDegrees(double angrad)
	弧度--》角度
### toRadians(double angdeg)
	角度——》弧度
## BigInteger
	不可变的任意精度的整数
### 代码示例
```java
BigInteger bi = new BigInteger(); 			 			 BigInteger("1243324112234324324325235245346567657653");
System.out.println(bi);
```
## BigDecimal
	精度比较高的浮点型数字

### 代码示例
```java
BigDecimal bd = new BigDecimal("12435.351");
BigDecimal bd2 = new BigDecimal("11");
System.out.println(bd.divide(bd2, BigDecimal.ROUND_HALF_UP));
System.out.println(bd.divide(bd2, 25, BigDecimal.ROUND_HALF_UP));
```


# 枚举类与注解
类的对象只有有限个，确定的。我们称此类为枚举类。

## 规则
	当需要定义一组常量时,强烈建议使用枚举类
	如果枚举类中只有一个对象,则可以作为单例模式的实现方式

## 应用
	jdk5.0之前,自定义枚举类
	jdk5.0,可以使用enum关键字定义枚举类
###  枚举类的实现
	实现接口,在枚举类中实现抽象方法
	枚举类的对象分别实现接口中的抽象方法
### 方法
	values()方法:返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值。
	valueOf(String str):可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的“名字”。
	toString():返回当前枚举类对象常量的名称


## 示例代码
```java
public class SeasonTest {
    public static void main(String[] args) {
        Season spring = Season.SPRING;
        System.out.println(spring);

    }
}

//自定义枚举类
class Season{
    //1.声明Season对象的属性:private final修饰
    private final String seasonName;
    private final String seasonDesc;

    //2.私有化类的构造器,并给对象属性赋值
    private Season(String seasonName,String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }
    
    //3.提供当前枚举类的多个对象：public static final的
    public static final Season SPRING = new Season("春天","春暖花开");
    public static final Season SUMMER = new Season("夏天","夏日炎炎");
    public static final Season AUTUMN = new Season("秋天","秋高气爽");
    public static final Season WINTER = new Season("冬天","冰天雪地");
    
    //4.其他诉求1：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }
    
    public String getSeasonDesc() {
        return seasonDesc;
    }
    //4.其他诉求1：提供toString()
    @Override
    public String toString() {
        return "Season{" +
                "seasonName='" + seasonName + '\'' +
                ", seasonDesc='" + seasonDesc + '\'' +
                '}';
    }
}
```

```java
/**
 * 使用enum关键字定义枚举类
 * 说明：定义的枚举类默认继承于java.lang.Enum类
 */
public class SeasonTest1 {
    public static void main(String[] args) {
        Season1 summer = Season1.SUMMER;
        //toString():返回枚举类对象的名称
        System.out.println(summer.toString());

//        System.out.println(Season1.class.getSuperclass());
        System.out.println("****************");
        //values():返回所有的枚举类对象构成的数组
        Season1[] values = Season1.values();
        for(int i = 0;i < values.length;i++){
            System.out.println(values[i]);
            values[i].show();
        }
        System.out.println("****************");
        Thread.State[] values1 = Thread.State.values();
        for (int i = 0; i < values1.length; i++) {
            System.out.println(values1[i]);
        }

        //valueOf(String objName):返回枚举类中对象名是objName的对象。
        Season1 winter = Season1.valueOf("WINTER");
        //如果没有objName的枚举类对象，则抛异常：IllegalArgumentException
//        Season1 winter = Season1.valueOf("WINTER1");
        System.out.println(winter);
        winter.show();
    }
}

interface Info{
    void show();
}

//使用enum关键字枚举类
enum Season1 implements Info{
    //1.提供当前枚举类的对象，多个对象之间用","隔开，末尾对象";"结束
    SPRING("春天","春暖花开"){
        @Override
        public void show() {
            System.out.println("春天在哪里？");
        }
    },
    SUMMER("夏天","夏日炎炎"){
        @Override
        public void show() {
            System.out.println("宁夏");
        }
    },
    AUTUMN("秋天","秋高气爽"){
        @Override
        public void show() {
            System.out.println("秋天不回来");
        }
    },
    WINTER("冬天","冰天雪地"){
        @Override
        public void show() {
            System.out.println("大约在冬季");
        }
    };

    //2.声明Season对象的属性:private final修饰
    private final String seasonName;
    private final String seasonDesc;

    //2.私有化类的构造器,并给对象属性赋值

    private Season1(String seasonName,String seasonDesc){
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    //4.其他诉求1：获取枚举类对象的属性
    public String getSeasonName() {
        return seasonName;
    }

    public String getSeasonDesc() {
        return seasonDesc;
    }
```
# 集合
## Collection接口
单列数据，定义了存取一组对象的方法的集合

![image-20220325175146470](E:\Typora\JavaSenior\image-20220325175146470.png)

	List 元素有序、可重复的集合,又被称为“动态数组”
	Set 元素无序、不可重复的集合
	Vector 线程安全的,效率低
	ArrayList 线程不安全的,效率高,底层使用object[]存储
	LinkedList 对于频繁的插入删除操作,存储效率最高,底层使用双向链表存储
	LinkedHashSet 在添加数据的同时,每个数据还维护了两个引用,记录此数据,对于频繁的遍历操作，效率更高         
	TreeSet 要求添加的数据,是相同类的对象

### ArrayList
作为List接口的主要实现类，线程不安全，效率高；底层使用Object[ ] element
#### 底层源码
```java
public ArrayList() {
	this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
/**
 * 创建一个初始容量为0的数组elementData.
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

//确保容量充足
public void ensureCapacity(int minCapacity) {
        if (minCapacity > elementData.length
            && !(elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
                 && minCapacity <= DEFAULT_CAPACITY)) {
            modCount++;
            grow(minCapacity);
        }
    }
    


//如果此次的添加导致底层数组容量不够,则需要扩容
private Object[] grow(int minCapacity) {
        int oldCapacity = elementData.length;
        if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            int newCapacity = ArraysSupport.newLength(oldCapacity,
                    minCapacity - oldCapacity, /* minimum growth */
                    oldCapacity >> 1           /* preferred growth */);
            return elementData = Arrays.copyOf(elementData, newCapacity);
        } else {
            return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];
        }
    }
```
总结：开发中使用带参的构造器, 会避免在中间扩容,jdk7中的ArrayList对象的创建类似于饿汉式，jdk8中的则类似于单例模式的懒汉式，延迟了数组的创建，节省内存。

### LinkedList
对于频繁的插入删除操作，使用此类效率比ArrayList效率高；底层使用双向链表存储
#### 底层源码
```java
/**
     * Pointer to first node.
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     */
    transient Node<E> last;

/**
     * Links e as first element.
     */
    private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
    }

/**
  * Links e as last element.
  */
void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }

```

### Vector
作为List接口的古老实现类：线程安全的，效率低下；底层使用Object[ ] elementData存储

### Set
存储无序的，不可重复的数据

### HashSet
作为Set接口的主要实现类 ，线程不安全的，可以存储null值

#### 特性
	无序性: 不等于随机性,存储的数据在底层数组中并非按照数组索引的顺序添加,而是根据数据的哈希值决定的
	不可重复性: 保证添加的元素按照equals方法判断,不能返回true

#### 底层原理
	在向HashSet中添加元素a,首先调用元素a所在类的hashCode()方法,计算元素a的哈希值
	使用散列函数(取模)计算出数组中的存放位置(索引位置)
	假如已有相同的模数索引,就按照equals()判断当前链表的元素是否重复,假如返回false,就存入当前链表
	jdk中（七上八下）版本七为新元素放在数组中,指向原来的元素(里面),版本八为新元素在链表边(外面)

#### 基本操作
	retainAll(Collection coll1) 交集,获取当前集合和其他集合的交集
	hashCode( ) 返回当前对象的哈希值
	集合——>数组 toArray()
	数组——>集合 Arrays.asList(new String[]{“AA”,”BB”})

### LinkedHashSet
HashSet的子类，在添加数据的同时，每个数据还有两个引用遍历其内部数据时，可以按照添加的顺序遍历

![image-20220329162450443](E:\Typora\JavaSenior\image-20220329162450443.png)




### TreeSet
可以按照添加对象的指定属性，进行排序

#### 规则
	要求是相同类的对象
#### 排序方式
	自然排序（实现Comparable接口）、定制排序（重写Compare方法）


## Map接口

双列数据,存储具有映射关系 key-value对 的集合



![image-20220325190113786](E:\Typora\JavaSenior\image-20220325190113786.png)

### HashMap
Map的主要实现类，线程不安全，但是效率高，可存储任何数据
#### 特点
	key 不可重复,且无序,使用Set存储,重写了equals()和hashCode()
	value 可重复的,使用Collection存储value,key无序,value也无序。重写了equals()

#### 底层原理
	数组+链表(JDK7)
	数组+链表+红黑树(JDK 8)

#### 重要常量
	DEFAULT_INITIAL_CAPACITY:默认容量，16
	MAXIMUM_CAPACITY:最大支持容量，2^30
	DEFAULT_LOAD_FACTOR：默认加载因子，为了使链表增加的次数少，加载因子尽可能小，数组利用率增加，加载因子增大，统计学之后，得出0.75f
	TREEIFY_THRESHOLD：链表长度大于该默认值，转化为红黑树
	table:存储元素的数组，总是2的n次幂
	entrySet:存储具体元素的集
	size:存储的键值对的数量

#### 底层源码

##### JDK7
	new HashMap() 底层创建了一个长度为16的Entry[] table
	首次调用key1所在类的hashCode()计算key1哈希值,此哈希值经过某种算法计算以后,得到在Entry数组中的存放位置
	如果key1的哈希值与已经存在的数据的哈希值都不相同,此时添加成功
	如果key1的哈希值与已经存在的数据的哈希值相同,调用key1所在类的equals()方法，
		如果返回false,则添加成功
		如果返回true,则使用新value替换旧value

```java
//底层创建了一个长度为16的Entry[] table
public HashMap(int initialCapacity, float loadFactor) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal initial capacity: " +
                                           initialCapacity);
    if (initialCapacity > MAXIMUM_CAPACITY)
        initialCapacity = MAXIMUM_CAPACITY;
    if (loadFactor <= 0 || Float.isNaN(loadFactor))
        throw new IllegalArgumentException("Illegal load factor: " +
                                           loadFactor);
    int capacity = 1;
    while(capacity < initialCapacity)
        capacity <<= 1;
    this.loadFactor = loadFactor;
    threshold = (int)Math.min(capacity * loadFactor,MAXIMUN_CAPACITY +1);
    table = new Entry[capacity];
}
```


```java
//put操作
public V put (K key,V value){
	if(key = null)
		return putForNullKey(value);
//计算哈希值
	int hash = hash(key);
	int i = indexFor(hash,table.length);
	for(Entry<K,V> e = table[i];e!=null;e=e.next){
		Object k;
		if(e.hash = hash&& ((k=e.key)==key||key.equals(k))){
            e.value = value;
            e.recordAccess(this);
            return oldValue;
		}
	}
	modCount++;
	addEntry(has,key,value,i);
	return null;
}
```




##### JDK8
	new HashMap() 底层没有创建一个长度为16的数组
	jdk8底层的数组是 Node [ ] 而非Entry[ ]
	首次调用put()底层创建为16的数组
	当数组的某一个索引位置的元素,它以链表形式存在的数据个数 > 8,且当数组长度 > 64
	此时索引位置上的所有数据改为使用红黑树存储

```java
//属性的赋值，没有创建数组
public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }
```

```java
//首先判断是否存在table数组，不存在则创建
 if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
```
```java
//创建数组
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```
```java
//存入数据
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //当链表长度超过8就会变成红黑树进行存储
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                   	//以链表形式存在着相同的数据,则break出当前for循环,否则进行for循环
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```




### LinkedHashMap

保证在遍历map元素时，可以按照添加顺序实现遍历

	在原有的HashMap底层基础上,添加了一对指针
	对于频繁的遍历操作,此类执行效率高

### TreeMap
按照key进行排序，实现排序遍历，底层使用红黑树—>二叉树—>二叉树排序，可以定制排序
#### 规则
	向TreeMap中添加数据时，要求key必须是由同一个类创建的对象

### Hashtable
	线程安全,效率低,不能存储null的key和value


### properties
	常用来处理配置文件，key和value都是String类型的

## Iterator接口
用于遍历Collection集合中的元素，又不暴露该对象的内部细节

	仅用于遍历集合，本身并不提

### 基本操作

	hasNext() 如果仍有元素可以迭代,则返回true
	next() 返回迭代的下一个元素
	remove() 从迭代器指向的 collection 中移除迭代器返回的最后一个元素

### 执行原理

![image-20220325221725844](E:\Typora\JavaSenior\image-20220325221725844.png)

## Collections工具类
	操作Collection和Map的工具类
### 基本操作
	reverse(List<?> list):反转指定列表中元素的顺序
	shuffle(List<?> list):使用默认随机源对指定列表进行置换
	sort(List<T> list):根据元素的自然顺序 对指定列表按升序进行排序
	swap(List<?> list, int i, int j):在指定列表的指定位置处交换元素
	max(Collection coll):根据元素的自然顺序，返回给定 collection 的最大元素
	frequency(Collection c, Object o):返回指定对象的元素数
	copy(List dest, List src):将所有元素从一个列表复制到另一个列表
	list(Enumeration e):返回一个数组列表，它按返回顺序包含指定枚举返回的元素



## 面试题：Collection和Collections的区别
	Collections是一个可以用来操作Collection的工具类

## 面试题：HashMap 和 Hashtable的异同

	Hashtable是线程安全的，HashMap是非线程安全的
	Hashtable的方法是同步的，HashMap的方法不是
	HashMap可以将空值作为一个表的key或value，HashMap的方法不是
	HashMap默认初始化值是16，之后每次扩充，容量变为原来的2倍
	Hashtable默认初始化值是11，之后每次扩充，容量变为原来的2n+1


## 面试题：CurrentHashMap 和 HashTable的异同
	CurrentHashMap为了在高并发场景下,执行效率更高,使用了分段锁机制。

# 泛型
	Generic,在定义类时通过标识类中的某个属性的类型,某个方法的返回值及参数类型。这个类型参数将在使用时确定。

## 示例代码
```java
public void testGeneric(){
    ArrayList<Integer> objects = new ArrayList<Integer>();
    objects.add(14);
    objects.add(74);
    objects.add(94);
    objects.add(78);
    //objects.add("深田");

    //方式二
    Iterator<Integer> iterator = objects.iterator();
    while (iterator.hasNext()){
        System.out.println(iterator.next());
    }
}
```

# IO流

input and output as streams


| 抽象基类     | 节点流           | 缓冲流               |
| ------------ | ---------------- | -------------------- |
| InputStream  | FileInputStream  | BufferedInputStream  |
| OutputStream | FileOutputStream | BufferedOutputStream |
| Reader       | FileReader       | BufferedReader       |
| Writer       | FileWriter       | BufferedWriter       |

注：InputStream用于读取二进制数据，Reader用于读取文本数据

## 常用方法

	Windows和Dos系统默认使用“\”
	Unix和Linux用“/”表示
	File.separator:不同系统的默认分隔符
	List():获取指定目录下的文件和文件夹
	listFiles():获取指定目录下的文件 File 类
	canRead():是否可读
	canWrite():是否可写
	createNewFile():创建文件。若文件存在，则不创建，返回false
	mkdir():创建文件目录
	mkdirs():创建文件目录，如果上级没有，则一并创建
	delete():删除文件，Java删除文件不走回收站

## 示例代码
```java
@Test
public void testBufferedStreamTest(){
	//创建File实例
	File srcFile = new File("911Mothers_2010W-480p.mp4");
	File destFile = new File("911Mothers_2010W-481p.mp4");
	//创建读文件实例
	FileInputStream fileInputStream = null;
	FileOutputStream fileOutputStream = null;
	BufferedInputStream bufferedInputStream = null;
	BufferedOutputStream bufferedOutputStream = null;
	try {
	fileInputStream = new FileInputStream(srcFile);
	bufferedInputStream = new BufferedInputStream(fileInputStream);
	//创建写文件实例
	fileOutputStream = new FileOutputStream(destFile);
	bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
	//读取的方法
	int read;
	byte[] buffer = new byte[1024];
	while ((read=bufferedInputStream.read(buffer))!=-1){
	//写入的方法
	bufferedOutputStream.write(buffer,0,read);
	}
		} catch (IOException e) {
            e.printStackTrace();
        } finally {

            //关闭缓冲流
            try {
                if (bufferedInputStream!=null)
                bufferedInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (bufferedInputStream!=null)
                bufferedOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```



















