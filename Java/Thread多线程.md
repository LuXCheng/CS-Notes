# Thread多线程

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
### 方法三、使用Callable创建多线程,JDK5.0新增
实现Callable接口定义多线程，重写call方法，并将该接口对象做参数传给FutureTask对象，再把FutureTask对象传给Thread对象，调用start();  
Callable定义的多线程功能更加丰富，其call方法可以返回一个对象；  
例如：  
```java
class SumThread implements Callable {

    @Override
    public Object call() throws Exception {
        int sum=0;
        for (int i = 1; i <= 100; i++) {
            System.out.println(Thread.currentThread().getName()+" "+i);
            sum+=i;
        }
        return sum;
    }
}

public class ThreadCallable {
    public static void main(String[] args) {
        SumThread thread = new SumThread();

        FutureTask futuretask = new FutureTask(thread);

        Thread t = new Thread(futuretask);
        t.setName("getSum");
        t.start();

        try {
            Object sum=futuretask.get();
            System.out.println("sum is " + sum);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```
### 方法四、创建线程池,JDK5.0新增

```java
class NumThread implements Runnable {

    @Override
    public void run() {
        for (int i = 1; i <= 100; i++) {
            if(i % 2 == 0) {
                System.out.println(Thread.currentThread().getName()+" "+i);
            }
        }
    }
}

public class ThreadPool {
    public static void main(String[] args) {

        ExecutorService service = Executors.newFixedThreadPool(10);

        ThreadPoolExecutor service1 = (ThreadPoolExecutor) service;

        service.execute(new NumThread());
    }
}
```
continue......
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
    
可以看到优先级高的主线程和子线程任然是交替执行，并不意味着高优先级就一定被先执行完，低优先级才执行，而是高优先级高概率执行；  

## 五、线程的生命周期

Thread.State中的定义，State，Thread一个内部类；  
生命周期的状态：  
```java
//内部类
public enum State {
        /*Thread state for a thread which has not yet started.*/
        /*线程还未开始执行*/
        NEW,

        /** Thread state for a runnable thread.  A thread in the runnable state is executing in the Java virtual machine but it may be waiting for other resources from the operating system such as processor. */
        /*具备运行条件，没有分配CPU资源*/
        RUNNABLE,

        /** Thread state for a thread blocked waiting for a monitor lock. A thread in the blocked state is waiting for a monitor lock to enter a synchronized block/method or reenter a synchronized block/method after calling {@link Object#wait() Object.wait}. */
        /*阻塞*/
        BLOCKED,

        WAITING,

        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
         /*死亡*/
        TERMINATED;
    }
```
运行--->阻塞  
wait(),sleep(),join(),等待同步锁synchronized,resume();  
阻塞--->就绪  
notify()/notifyAll()，sleep()结束，join()结束，获取同步锁synchronized，suspend();  
## 六、线程同步安全问题  
### （一）同步代码块
#### 1.继承Thread类的线程同步处理
**窗口买票问题**，当直接创建三个线程运行会出现问题，使用synchronized锁解决；  

- 使用synchronized包含住造作共享数据的代码块；  
- 创建同步监视器，即锁，任何类的对象都可以作为锁；  

同步监视器必须是static即类对象，因为当继承Thread类实现多线程时，会创造多个线程，如果没有定义为静态对象，则会出现每个线程创建一个锁，使得同步锁没有意义；  
同步监视器也可以为当前类的类变量，即类名.class()，如例子中的Ticket.class();  

示例：  
```java
class Ticket extends Thread {

    public static int ticket = 100;
    public static final Object obj = new Object();

    @Override
    public void run() {
        while(true) {
            synchronized(obj) {
                if (ticket > 0) {
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + " " + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}
```
#### 2.实现Runnable接口的多线程同步安全处理
与继承Thread类的多线程不同的是，此时只需要定义任意一个类的对象；  
同步监视器可以使用this；  
```java
class Ticket implements Runnable {

    private int ticket = 100;
    final Object obj = new Object();

    @Override
    public void run() {
        while(true) {
            synchronized(obj) {
                if(ticket>0) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + " " + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}
```
### （二）同步方法
定义同步方法仍会使用同步监视器，不用我们声明；  

#### 1.实现接口
定义synchronized方法，此时方法中有this类对象同步监视器；  
```java
class Ticket implements Runnable {

    private int ticket = 100;
    final Object obj = new Object();

    @Override
    public void run() {
        while(true) {
            sellOut();
        }
    }

    public synchronized void sellOut() {
        if(ticket>0) {
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(Thread.currentThread().getName() + " " + ticket);
            ticket--;
        }
    }
}
```
#### 2.继承Runnable
此时定义方法时，需将方法定义为static，此时监视器为当前类本身，即public static synchronized void sellOut()；  
### （三）Lock锁解决线程安全问题，JDK5.0新增
示例：  
```java
class Ticket implements Runnable {

    private int ticket = 100;

    private ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while(true) {
            try {
                lock.lock();
                if (ticket > 0) {
                    System.out.println(Thread.currentThread().getName() + " " + ticket);
                    ticket--;
                } else {
                    break;
                }
            } finally {
                lock.unlock();
            }
        }

    }
}
```
定义ReentrantLock对象，完成线程安全问题；  
  
  
**synchronized和Lock的区别**  

1.synchronized是隐式锁，在执行完同步代码后，自动释放同步监视器；  
2.Lock是显示锁，需要手动启动同步：.lock()和手动结束同步：.unlock();  

## 七、线程的通信
### 方法
当需要多个线程交替执行时，采用线程通信，对同步监视器进行操作；  
**wait()**  
此方法阻塞当前正在执行的线程，并释放同步锁，使其他线程开始获得锁，  
**notify()/notifyAll()**  
唤醒因wait()操作而阻塞的线程；  
```java
class Print implements Runnable {

    private int number = 1;
    private final Object obj = new Object();

    @Override
    public void run() {
        while(true) {
            synchronized (obj) {
                obj.notify();
                if(number <= 100) {
                    System.out.println(Thread.currentThread().getName()+" "+number);
                    number++;
                } else {
                    break;
                }

                try {
                    obj.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```
### wait()和sleep()的区别？
同：  

- 都会使当前线程阻塞

异：  

- 1.wait()定义在Object类中，sleep定义在Thread类  
- 2.当在同步代码块或同步方法中时，wait()执行后会释放当前同步监视器，sleep()不会；  
- 3.sleep()可在任意一处使用，而wait()只能在同步方法或代码块中使用：  
