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
## String类
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


### replace
replace(char oldChar, char newChar) 

	返回一个新的字符串，通过用 newChar 替换此字符串中出现的所有 oldChar 得到的



## JDK8前的date类

## JDK8后的date类
## 比较器
## System类
## Math类
## BigInteger
## BigDecimal

