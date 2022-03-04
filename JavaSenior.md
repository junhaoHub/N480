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
	俗称锁.任何一个类的锁都可以充当锁
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
	当且仅当此字符串包含指定的 char 值序列时，返回 true
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
	返回字符串的副本，忽略前导空白和尾部空白 

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
	集合存储的元素的特性，如果元素是可比较的则可以进行相应的排序，否则不行
### 优点
	Java中的对象，正常情况下，只能进行比较: ==  或 != 。不能使用 > 或 < 的
	但是在开发场景中，我们需要对多个对象进行排序，言外之意，就需要比较对象的大小

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
	当需要定义一组常量时，强烈建议使用枚举类
	如果枚举类中只有一个对象，则可以作为单例模式的实现方式

## 应用
	jdk5.0之前,自定义枚举类
	jdk5.0,可以使用enum关键字定义枚举类
###  枚举类的实现
	实现接口，在枚举类中实现抽象方法
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