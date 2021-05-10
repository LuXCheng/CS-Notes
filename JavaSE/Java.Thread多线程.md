# Java.Thread多线程

---


## 一、程序、进程、线程
1.程序是静态的代码，进程是程序的一次执行过程；  
2.线程指的是进程的一个执行流，表示一条执行路径，是程序运行的最小单位，是CPU调度和分配的基本单位；  
3.一个进程有多个线程组成，且各线程能够共享进程的所有资源，每个线程都拥有自己的堆空间和程序计数器，线程能够实现并发进行不同任务；    
区别：  


|   程序      |  进程  |  线程  |  
| :----: | :----: |  :----: |
| .  | 操作系统资源分配和调度的基本单位|任务调度和执行的基本单位|
| .|独立空间| 独立堆栈空间和程序计数器|
|. |各进程相互独立 | 线程之间资源共享|


------
## 二、Thread多线程的创建
### 方法一、继承Thread类
1.Thread类在java.lang包下，用来定义一个多线程；  
2.Thread类通过继承实现，重写run()方法完成操作，start()方法启动线程；  
3.例如（体会多线程执行过程）：  
```java
class MyThread extends Thread {

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if(i%2==0) {
                System.out.println(i+" "+Thread.currentThread().getName());
            }
        }
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyThread thread=new MyThread();
        thread.start();
//        thread.run();
//        thread.start();
        MyThread thread2=new MyThread();
        thread2.start();
        System.out.println("hello");
        System.out.println("hello");
        System.out.println("hello");
        System.out.println("hello");
    }
}
```
### 方法二、创建Thread匿名子类
```java
public class ThreadTest {
    public static void main(String[] args) {
        new Thread(){
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    if(i%2!=0) {
                        System.out.println(i+" "+Thread.currentThread().getName());
                    }
                }
            }
        }.start();
    }
}
```
注： 
1.不能直接调用run()方法，这只能表示调用了此方法，而没有启动该线程；  
2.创建多线程对象和对象的start都是通过main的主线程完成的，只有当定义的线程启动后线程才开始执行：  
3.一个线程对象不能start()多个，在Thread类中使用了信号量threadStatus完成互斥操作；  
```java
if (threadStatus != 0)
            throw new IllegalThreadStateException();
```

## 三、Thread中常用方法
1.start();  
启动线程，调用run()方法；  
2.run();  
重写，将需要执行的操作声明于此；  
3.currentThread();  
返回当前线程；  
4.getName();  
获取线程名字；  
5.setName("String");  
设置线程名字； 
java设置默认名字：  
```java
private static int threadInitNumber;
    private static synchronized int nextThreadNum() {
        return threadInitNumber++;
    }
    
    public Thread() {
        init(null, null, "Thread-" + nextThreadNum(), 0);
    }
```
6.yield();  
释放CPU，将控制权交给其他线程；  
7.join();  
阻塞当前进程，让其他线程执行，且执行完成后再继续执行；  
8.sleep(long millitime);  
阻塞该线程，时间millitime毫秒；  
9.stop();   
强制终止线程，不建议使用；  
10.isAlive();  
判断线程是否存活；  
## 四、线程的优先级
### （一）线程的调度
1.先到先服务，采用时间片轮转方式；  
2.高优先级先服务；  
### （二）线程的优先级
Thread中定义了最大、最小、默认优先级值；  
最大10，最小1，默认5；  
```java
    /**
     * The minimum priority that a thread can have.
     */
    public final static int MIN_PRIORITY = 1;

   /**
     * The default priority that is assigned to a thread.
     */
    public final static int NORM_PRIORITY = 5;

    /**
     * The maximum priority that a thread can have.
     */
    public final static int MAX_PRIORITY = 10;
```
### （三）关于线程优先级的方法
1.getPriority();  
返回当前线程优先级，
2.setPriiority(int priority);  
设置线程优先级，  
例如：  
```java
class MyThread extends Thread {

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if(i%2==0) {
                System.out.println(i+" "+Thread.currentThread().getName()+" "+getPriority());
            }
        }
    }
}

public class ThreadTest {
    public static void main(String[] args) {
        MyThread thread=new MyThread();

        thread.setPriority(Thread.MAX_PRIORITY);
        thread.start();

        Thread.currentThread().setPriority(Thread.MIN_PRIORITY);
        for (int i = 0; i < 100; i++) {
            if(i%2!=0) {
                System.out.println(i+" "+Thread.currentThread().getName()+" "+Thread.currentThread().getPriority());
            }
        }
    }
}
```
部分运行结果：  

    0 Thread-0 10
    1 main 1
    2 Thread-0 10
    3 main 1
    4 Thread-0 10
    5 main 1
    6 Thread-0 10
    7 main 1
    8 Thread-0 10
    9 main 1
    10 Thread-0 10
    11 main 1
    13 main 1
    15 main 1
    12 Thread-0 10
    14 Thread-0 10
    16 Thread-0 10
    
可以看到优先级高的主线程和子线程任然是交替执行，并不意味着高优先级就一定被先执行，高优先级高概率执行；  
