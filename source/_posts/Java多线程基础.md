---
title: Java多线程基础
date: 2021-08-09 10:27:54
tags: Java
categories: 学习笔记
---

**JAVA多线程基础**

# 线程创建

三种创建方式

1.继承Thread类（重点）

2.实现Runnable接口（重点）

3.实现Callable接口（了解）

> 继承Thread类

自定义线程类继承Thread类

重写run()方法，编写线程执行体

创建线程对象，调用start()方法启动线程（<font color=#FF0000>不是调用run()方法</font>）

<font color=#FF0000>不建议使用：避免OOP单继承局限性</font>

```java
//创建线程方式一：继承Thread类，重写run()方法，调用start开启线程
public class TestThread1 extends Thread{

    @Override
    public void run() {
        //run方法线程体
        for (int i = 0; i < 200; i++) {
            System.out.println("我在看代码---" + i);
        }
    }

    public static void main(String[] args) {
        //main线程，主线程

        //创建一个线程对象
        TestThread1 testThread1 = new TestThread1();
        //调用start方法开启线程
        testThread1.start();

        for (int i = 0; i < 1000; i++) {
            System.out.println("我在学习多线程--" + i);
        }
    }
}      
```

> 实现Runnable接口

定义MyRunnable类实现Runnable接口

实现Run()方法，编写线程执行体

创建线程对象，调用start()方法启动线程

<font color=#FF0000>推荐使用：避免单继承局限性，灵活方便，方便同一个对象被多个线程使用</font>

```java
//创建线程方式2：实现runnable接口，重写run()方法，执行线程需要丢入runnable接口实现类，调用start方法。
public class TestThread2 implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("我在看代码---"+i);
        }
    }                     

    public static void main(String[] args) {
        //创建runnable接口的实现类对象
        TestThread2 testThread2 = new TestThread2();
        //创建线程对象，通过线程对象来开启我们的线程，代理
//        Thread thread = new Thread(testThread2);
//        thread.start();
        new Thread(testThread2).start();

        for (int i = 0; i < 1000; i++) {
            System.out.println("我在学习多线程--"+i);
        }
    }
}
```

> 实现Callable接口

创建一个实现Callable的实现类。
实现call()方法，将此线程需要执行的操作声明在call()中。
创建Callable接口实现类的对象。
将此Callable接口实现类的对象作为传递到FutureTask构造器中，创建FutureTask的对象。
将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()方法。

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class CallableTest {
    public static void main(String[] args) {
        CallableThread callableThread = new CallableThread();

        FutureTask futureTask = new FutureTask(callableThread);

        Thread t1 = new Thread(futureTask);

        t1.start();

        try {
            //get()方法的返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值
            Object sum = futureTask.get();
            System.out.println(sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

class CallableThread implements Callable{

    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 0; i <= 100; i++) {
            if (i % 2 ==0){
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}
```



# Lamda表达式

函数式接口的定义：

任何接口，如果只包含唯一一个抽象方法，那么它就是一个函数式接口。

对于函数式接口，我们可以通过lambda表达式来创建该接口的对象。

# 线程方法

| 方法                           | 说明                                           |
| ------------------------------ | ---------------------------------------------- |
| setPriority(int newPriority)   | 更改线程的优先级                               |
| static void sleep(long millis) | 在指定的毫秒数内让当前正在执行的线程休眠       |
| void join()                    | 等待该线程终止（等待调用此方法的线程执行完毕） |
| static void yield()            | 暂停当前正在执行的线程对象，并执行其他线程     |
| void interrupt()               | 中断线程                                       |
| boolean isAlive()              | 测试线程是否处于活动状态                       |

# 线程停止

```java
//测试stop
//1.建议线程正常停止--->利用次数，不建议死循环
//2.建议使用标志位--->设置一个标志位
//3.不要用stop或destory等过时或者JDK不建议使用的方法
public class TestStop implements Runnable{

    //设置一个标志位
    private boolean flag = true;

    @Override
    public void run() {
        int i = 0;
        while(flag){
            System.out.println("run...Thread" + i++);
        }
    }

    public void stop(){
        this.flag = false;
    }

    public static void main(String[] args) {
        TestStop testStop = new TestStop();

        new Thread(testStop).start();

        for (int i = 0; i < 1000; i++) {
            System.out.println("main" + i);
            if(i==900){
                testStop.stop();
                System.out.println("线程停止了");
            }
        }
    }
}
```

# 线程休眠

+ sleep(时间)指定当前线程阻塞的毫秒数
+ sleep存在异常InterruptedException
+ sleep时间达到后线程进入就绪状态
+ sleep可以模拟网络延时，倒计时等。
+ 每一个对象都有一个锁，sleep不会释放锁。

```java
//10秒倒计时
public class TestSleep{

    public static void main(String[] args) {
        try {
            tenDown();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void tenDown() throws InterruptedException {
        int num = 10;
        while(true){
            Thread.sleep(1000);
            System.out.println(num--);
            if (num<=0){
                break;
            }
        }
    }
}

```

# 线程礼让

+ 礼让线程，让当前正在执行的线程暂停，但不阻塞
+ 将线程从运行状态转为就绪状态
+ 让CPU重新调度，礼让不一定成功，看CPU调度

```java
public class TestYield {

    public static void main(String[] args) {
        MyYield myYield = new MyYield();

        new Thread(myYield, "a").start();
        new Thread(myYield, "b").start();
    }
}

class MyYield implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + "线程开始执行");
        Thread.yield();
        System.out.println(Thread.currentThread().getName() + "线程停止执行");
    }
}
```

# Join

//等待线程执行完毕

自己的理解：等待调用此方法的线程执行完毕。

# 观测线程状态

Thread.State

+ **NEW**

  尚未启动的线程处于此状态。

+ **RUNNABLE**

  在Java虚拟机中执行的线程处与此状态。

+ **BLOCKED**

  被阻塞等待监视器锁定的线程处于此状态。

+ **WAITING**

  正在等待另一个线程执行特定动作的线程处于此状态。

+ **TIMED_WAITING**

  正在等待另一个线程执行动作达到指定等待时间的线程处于此状态。

+ **TERMINATED**

  已退出的线程处于此状态。

```java
public class TestState {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("//////");
        });

        //观察状态
        Thread.State state = thread.getState();
        System.out.println(state);//NEW

        //观察启动后
        thread.start();
        state =  thread.getState();
        System.out.println(state);//Run

        while(state != Thread.State.TERMINATED){//只要线程不终止，就一直输出状态
            Thread.sleep(100);
            state = thread.getState();
            System.out.println(state);
        }

    }
}
```

# 线程优先级

+ Java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度哪个线程来执行。
+ 线程的优先级用数字表示，范围从1~10
  + Thread.MIN_PRIORITY=1
  + Thread.MAX_PRIORITY=10
  + Thread.NORM_PRIORITY=5
+ 使用以下方式改变或获取优先级
  + getPriority()，setPriority(int xxx)

```java
//测试线程的优先级
public class TestPriority {

    public static void main(String[] args) {
        //主线程默认优先级
        System.out.println(Thread.currentThread().getName()+"-->"+Thread.currentThread().getPriority());

        MyPriority myPriority = new MyPriority();

        Thread t1 = new Thread(myPriority);
        Thread t2 = new Thread(myPriority);
        Thread t3 = new Thread(myPriority);
        Thread t4 = new Thread(myPriority);
        Thread t5 = new Thread(myPriority);
        Thread t6 = new Thread(myPriority);

        //先设置优先级，再启动
        t1.start();

        t2.setPriority(1);
        t2.start();

        t3.setPriority(4);
        t3.start();

        t4.setPriority(Thread.MAX_PRIORITY);
        t4.start();
/*
        t5.setPriority(-1);
        t5.start();

        t6.setPriority(11);
        t6.start();*/

    }

}

class MyPriority implements Runnable{

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"-->"+Thread.currentThread().getPriority());
    }
}
```

# 守护(daemon)线程

+ 线程分为<font color="FF0000">用户线程</font>和<font color="FF0000">守护线程</font>
+ 虚拟机必须确保用户线程执行完毕
+ 虚拟机不用等待守护线程执行完毕
+ 如后台记录操作日志，监控内存，垃圾回收等

```java
//测试守护线程
public class TestDaemon {
    public static void main(String[] args) {
        God god = new God();
        You you = new You();

        Thread thread = new Thread(god);
        thread.setDaemon(true);//默认是false表示是用户线程，正常的线程都是用户线程

        thread.start();//守护线程启动

        new Thread(you).start();//用户线程启动
    }
}

class God implements Runnable{

    @Override
    public void run() {
        while (true){
            System.out.println("上帝保佑着你");
        }
    }
}

class You implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 36500; i++) {
            System.out.println("你一生都开心地活着");
        }
        System.out.println("====goodbye!world!====");
    }
}
```

# 线程同步

多个线程操作同一个资源

由于同一进程的多个线程共享同一块存储空间，在带来方便的同时，也带来了访问冲突问题，为了保证数据在方法中被访问时的正确性，在访问时加入锁机制`synchronized`，当一个线程获得对象的排它锁，独占资源，其他线程必须等待，使用后释放锁即可。存在以下问题：

+ 一个线程持有锁会导致其他所有需要此锁的线程挂起；
+ 在多线程竞争下，加锁，释放锁会导致比较多的上下文切换和调度延时，引起性能问题。
+ 如果一个优先级高的线程等待一个优先级低的线程释放锁会导致优先级倒置，引起性能问题。

> 同步方法

+ 由于我们可以通过private关键字来保证数据对象只能被方法访问，所以我们只需要针对方法提出一套机制，这套机制就是`synchronized`方法和`synchronized`块

```
//同步方法
public synchronized void method(int args){}
```

+ synchronized方法控制对“对象”的访问，每个对象对应一把锁，每个synchronized方法都必须获得调用该方法的对象的锁才能执行，否则线程会阻塞，方法一旦执行，就独占该锁，直到该方法返回才释放锁，后面被阻塞的线程才能获得这个锁，继续执行。（缺陷：若将一个大的方法申明为synchronized将会影响效率）

> 同步块

+ 同步块：synchronized(Obj){}
+ Obj称之为**同步监视器**
  + Obj可以是任何对象，但是推荐使用共享资源作为同步监视器
  + 同步方法中无需指定同步监视器，因为同步方法的同步监视器就是this，就是这个对象本身，或者是class
+ 同步监视器的执行过程
  1. 第一个线程访问，锁定同步监视器，执行其中的代码。
  2. 第二个线程访问，发现同步监视器被锁定，无法访问。
  3. 第一个线程访问完毕，解锁同步监视器。
  4. 第二个线程访问，发现同步监视器没有锁，然后锁定并访问。

# 死锁

多个线程各自占有一些共享资源，并且互相等待其他线程占有的资源才能运行，而导致两个或者多个线程都在等待对方释放资源的情形。某个同步块同时拥有“两个以上对象的锁”时，就可能发生“死锁”的问题。

产生死锁的四个必要条件：

1. 互斥条件：一个资源每次只能被一个进程使用。
2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件：进程已获得的资源，在未使用完之前，不能强行剥夺。
4. 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

只要破环其中任意一个或多个条件就可以避免死锁的发生。

# Lock（锁）

+ 从JDK5.0开始，Java提供了更强大的线程同步机制——通过显式定义同步锁对象来实现同步。同步锁使用Lock对象充当。
+ java.util.concurrent.locks.Lock接口是控制多个线程对共享资源进行访问的工具。锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应先获得Lock对象。
+ ReentrantLock类实现了Lock，它拥有与synchronized相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock（可重入锁），可以显式加锁、释放锁。

```java
class A{
    private final ReentrantLock lock = new ReenTrantLock();
    public void m(){
        lock.lock();
        try{
            //保证线程安全的代码
        }
        finally{
            lock.unlock();
            //如果同步代码有异常，要将unlock()写入finally语句块
        }
    }
}
```

> synchorized与Lock的对比

+ Lock是显式锁（手动开启和关闭锁，别忘记关闭锁）synchronized是隐式锁，出了作用域自动释放。
+ Lock只有代码块锁，synchronized有代码块锁和方法锁
+ 使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）
+ 优先使用顺序：
  + Lock > 同步代码块（已经进入了方法体，分配了相应资源）> 同步方法（在方法体之外）

# 线程协作

> 线程通信

`Java提供了几个方法解决线程之间的通信问题

| 方法名             | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| wait()             | 表示线程一直等待，直到其他线程通知，与sleep不同，会释放锁。  |
| wait(long timeout) | 指定等待的毫秒数。                                           |
| notify()           | 唤醒一个处于等待状态的线程。                                 |
| notifyAll()        | 唤醒同一个对象上所有调用wait()方法的线程，优先级别高的线程优先调度。 |

<font color="FF0000">注意：均是Object类的方法，都只能在同步方法或者同步代码块中使用，否则会抛出异常`IllegalMonitorStateException`</font>

**解决方式一：**

并发协作模型“生产者/消费者模式”-->管程法

+ 生产者：负责生产数据的模块（可能是方法，对象，线程，进程）；
+ 消费者：负责处理数据的模块（可能是方法，对象，线程，进程）；
+ 缓冲区：消费者不能直接使用生产者的数据，他们之间有“缓冲区”

生产者将生产好的数据放入缓冲区，消费者从缓冲区拿出数据

**解决方式二：**

并发协作模型“生产者/消费者模式”--->信号灯法

# 线程池

+ 背景：经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大。
+ 思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。
+ 好处：
  + 提高相应速度（减少了创建新线程的时间）
  + 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
  + 便于线程管理
    + corePoolSize：核心池的大小
    + maximumPoolSize：最大线程数
    + keepAliveTime：线程没有任务时最多保持多长时间后会终止

> 使用线程池

+ JDK 5.0起提供了线程池相关API：ExecutorService和Executors
+ ExecutorService：真正的线程池接口。常见子类ThreadPoolExecutor
  + void execute(Runnable command)：执行任务/命令，没有返回值，一般用来执行Runnable
  + \<T>Future\<T> submit(Callable\<T> task)：执行任务，有返回值，一般又来执行Callable
  + void shutdown()：关闭连接池
+ Executors：工具类、线程池的工厂类，用于创建并返回不同类型的线程池

```java
//测试线程池
public class TestPool {
    public static void main(String[] args) {
        //1.创建服务，创建线程池
        ExecutorService service = Executors.newFixedThreadPool(10);

        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());
        service.execute(new MyThread());

        //2.关闭连接
        service.shutdown();
    }
}

class MyThread implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

# [JUC并发编程（链接）](E:\study\markdown笔记\JUC并发编程)

