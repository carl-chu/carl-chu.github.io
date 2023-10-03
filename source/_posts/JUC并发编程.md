---
title: JUC并发编程
date: 2022-04-21 11:22:00
tags: Java
categories: 学习笔记
index_img: /img/java_logo.jpg
---



# [JAVA多线程基础（链接）](E:\study\markdown笔记\JAVA多线程基础)

**JUC并发编程**

# 1.JUC概述

## 1.1 什么是JUC

JUC是`java.util.concurrent`工具包的简称，是一个处理线程的工具包，JDK1.5开始出现。

## 1.2 线程和进程的概念

**进程(Process)**是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础。在早期面向进程设计的计算机结构中，进程是程序的基本执行实体；在当代面向线程设计的计算机结构中，进程是线程的容器。程序是指令、数据及其组织形式的描述，进程是程序的实体。

**线程**（英语：thread）是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。

## 1.3 线程的状态

### 1.3.1 线程状态枚举类

```java
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,(新建)

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,（准备就绪）

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,（阻塞）

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,（不见不散）

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,（过时不候）

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;（终结）
    }
```

### 1.3.2 wait/sleep的区别

（1）sleep是Thread的静态方法，wait是Object的方法，任何对象实例都能调用。

（2）sleep不会释放锁，它也不需要占用锁。wait会释放锁，但调用它的前提是当前线程占有锁（即代码要在`synchronized`中）

（3）它们都可以被interrupted方法中断。

## 1.4 并发与并行

### 1.4.1 串行模式

串行表示所有任务都一一按先后顺序进行。

串行是一次只能取得一个任务，并执行这个任务。

### 1.4.2 并行模式

并行意味着可以同时取得多个任务，并同时去执行所取得的这些任务。

**并发**：同一时刻多个线程访问同一个资源，多个线程对一个点。

**并行**：多项工作一起执行，之后再汇总。

## 1.5 管程

Monitor 监视器  锁

一种同步机制，保证同一时间只有一个线程访问被保护数据或代码。

## 1.6 用户线程和守护线程



# 2.Lock接口

### 2.1 Synchronized

### 2.2 什么是Lock

Lock锁实现提供了比使用同步方法和语句可以获得的更广泛的锁操作。它们允许更灵活的结构，可能具有非常不同的属性，并且可能支持多个关联的条件对象。Lock提供了比synchronized更多的功能。

Lock与Synchronized区别

+ Lock不是Java语言内置的，`synchronized`是Java语言的关键字，因此是内置特性。Lock是一个类，通过这个类可以实现同步访问；
+ Lock和synchronized有一点非常大的不同，采用synchorized不需要用户手动释放锁，当synchronized方法或者synchronized代码块执行完之后，系统会自动让线程释放对锁的占用；而Lock则必须要用户去手动释放锁，如果没有主动释放锁，就有可能导致出现死锁现象。
+ Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断。
+ 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
+ Lock可以提高多个线程进行读操作的效率。

在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。



# 3.线程间通信

## 防止虚假唤醒问题

等待应该总是出现在循环中，就像这样： 

```java
  synchronized (obj) {
         while (<condition does not hold>)
             obj.wait(timeout);
         ... // Perform action appropriate to condition
     }
```

如果使用if进行判断，判断完进入了if代码块后调用了wait，之后被唤醒后代码会继续往下进行，从而不会再进入if语句进行判断，因此需要将if改为while。

# 4.线程间定制化通信

AA线程每轮打印5次，BB线程每轮打印10次，CC线程每轮打印15次，共打印10轮。

```java
// 创建资源类
class ShareResource {
    // 定义标志位
    private int flag = 1;
    //创建lock锁
    private Lock lock = new ReentrantLock();

    // 创建Condition
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();

    //打印5次
    public void print5(int loop) throws InterruptedException {
        //上锁
        lock.lock();
        try {
            //判断，防止虚假唤醒，使用while
            while (flag != 1){
                c1.await();
            }
            // 打印5次
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName()+"::"+i+":轮数:"+loop);
            }
            //通知
            flag = 2;
            c2.signal();
        }finally {
            //释放锁
            lock.unlock();
        }
    }

    //打印10次
    public void print10(int loop) throws InterruptedException {
        //上锁
        lock.lock();
        try {
            //判断，防止虚假唤醒，使用while
            while (flag != 2){
                c2.await();
            }
            // 打印10次
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName()+"::"+i+":轮数:"+loop);
            }
            //通知
            flag = 3;
            c3.signal();
        }finally {
            //释放锁
            lock.unlock();
        }
    }

    //打印15次
    public void print15(int loop) throws InterruptedException {
        //上锁
        lock.lock();
        try {
            //判断，防止虚假唤醒，使用while
            while (flag != 3){
                c3.await();
            }
            // 打印15次
            for (int i = 0; i < 15; i++) {
                System.out.println(Thread.currentThread().getName()+"::"+i+":轮数:"+loop);
            }
            //通知
            flag = 1;
            c1.signal();
        }finally {
            //释放锁
            lock.unlock();
        }
    }
}

public class ThreadDemo {
    public static void main(String[] args) {
        ShareResource shareResource = new ShareResource();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    shareResource.print5(i);
                } catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
        }, "AA").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    shareResource.print10(i);
                } catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
        }, "BB").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    shareResource.print15(i);
                } catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
        }, "CC").start();
    }
}
```



# 5.集合的线程安全

## 5.1 ArrayList的线程不安全

ArrayList是线程不安全的

解决方案：

+ Vector（使用了synchronized关键字），方案古老，使用频率不高
+ Collections.synchronizedList，方案古老，使用频率不高
+ CopyOnWriteArrayList（JUC提供，写时复制技术）

## 5.2 HashSet线程不安全

解决方案：

+ CopyOnWriteArraySet

## 5.3 HashMap线程不安全

解决方案：

+ ConcurrentHashMap

```java
// 集合线程不安全
public class ThreadDemo2 {
    public static void main(String[] args) {
        // 创建ArrayList集合
        // List<String> list = new ArrayList<>();

        // Vector解决
        // List<String> list = new Vector<>();

        // Collections解决
        // List<String> list = Collections.synchronizedList(new ArrayList<>());

        // CopyOnWriteArrayList解决
        // List<String> list = new CopyOnWriteArrayList<>();
        // for (int i = 0; i < 30; i++) {
        //     new Thread(()->{
        //         // 向集合中添加内容
        //         list.add(UUID.randomUUID().toString().substring(0,8));
        //         // 从集合中获取内容
        //         System.out.println(list);
        //     }, String.valueOf(i)).start();
        // }

        // 演示Hashset
        // Set<String> set = new HashSet<>();

        // Set<String> set = new CopyOnWriteArraySet<>();
        // for (int i = 0; i < 30; i++) {
        //     new Thread(()->{
        //         // 向集合中添加内容
        //         set.add(UUID.randomUUID().toString().substring(0,8));
        //         // 从集合中获取内容
        //         System.out.println(set);
        //     },String.valueOf(i)).start();
        // }

        // 演示HashMap
        // Map<String, String> map = new HashMap<>();
        Map<String, String> map = new ConcurrentHashMap<>();
        for (int i = 0; i < 30; i++) {
            String key = String.valueOf(i);
            new Thread(()->{
                // 向集合中添加内容
                map.put(key, UUID.randomUUID().toString().substring(0,8));
                // 从集合中获取内容
                System.out.println(map);
            },String.valueOf(i)).start();
        }
    }
}
```

# 6.多线程锁

## 6.1 锁的8种情况

[锁的8种情况](F:\study\my-practice\common\src\main\java\com\czh\common\multiThread\lock\Lock_8.java)

synchronized实现同步的基础：Java中的每一个对象都可以作为锁。

具体表现为以下3种形式：

+ 对于普通同步方法，锁是当前实例对象。
+ 对于静态同步方法，锁是当前类的class对象。
+ 对于同步方法块，锁是synchronized括号里配置的对象。

## 6.2 公平锁和非公平锁

new ReentrantLock(false)，设为false为非公平锁，不设默认为false。

new ReentrantLock(true)，设为true为公平锁。

**公平锁**：多个线程按照申请锁的顺序去获得锁，线程会直接进入队列去排队，永远都是队列的第一位才能得到锁。

- 优点：所有的线程都能得到资源，不会饿死在队列中。
- 缺点：吞吐量会下降很多，队列里面除了第一个线程，其他的线程都会阻塞，cpu唤醒阻塞线程的开销会很大。

**非公平锁**：多个线程去获取锁的时候，会直接去尝试获取，获取不到，再去进入等待队列，如果能获取到，就直接获取到锁。

+ 优点：可以减少CPU唤醒线程的开销，整体的吞吐效率会高点，CPU也不必取唤醒所有线程，会减少唤起线程的数量。

- 缺点：你们可能也发现了，这样可能导致队列中间的线程一直获取不到锁或者长时间获取不到锁，导致饿死。

## 6.3 可重入锁（递归锁）

广义上的可重入锁指的是可重复可递归调用的锁，在外层使用锁之后，在内层仍然可以使用，并且不发生死锁（前提得是同一个对象或者class），这样的锁就叫做可重入锁。

synchronized（隐式）和lock（显式）都是可重入锁。

### 6.4 死锁

死锁是指两个或两个以上的进程在执行过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。

```java
public class DeadLock {

    // 创建两个对象
    static Object a = new Object();
    static Object b = new Object();

    public static void main(String[] args) {
        new Thread(()->{
            synchronized (a){
                System.out.println(Thread.currentThread().getName()+" 持有锁a，试图获取锁b");
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (b){
                    System.out.println(Thread.currentThread().getName()+" 获取锁b");
                }
            }
        },"A").start();

        new Thread(()->{
            synchronized (b){
                System.out.println(Thread.currentThread().getName()+" 持有锁b，试图获取锁a");
                try {
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (a){
                    System.out.println(Thread.currentThread().getName()+" 获取锁a");
                }
            }
        },"B").start();
    }

}
```

**验证是否是死锁**

（1）jps

（2）jstack（jvm自带堆栈跟踪工具）

使用`jps -l`命令查看进程号，使用`jstack 进程号`命令查看是否存在死锁。

# 7.Callable接口

创建线程的方法：

+ 通过继承Thread类
+ 实现Runnable接口创建线程
+ 实现Callable接口
+ 线程池方式

Runable缺少一项功能，当线程终止时（即run()完成时），无法使线程返回结果。为支持此功能，Java提供了Callable接口。

Callable接口的特点如下：

+ 为了实现Runnable，需要实现不返回任何内容的run()方法，而对于Callable接口，需要实现在完成时返回结果的call()方法。
+ call()方法可以引发异常，而run()则不能。
+ 为实现Callable而必须重写call方法。

```java
class MyThread1 implements Runnable{

    @Override
    public void run() {

    }
}

class MyThread2 implements Callable{

    @Override
    public Integer call() throws Exception {
        return 200;
    }
}

public class Demo1 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // Runnable接口创建线程
        new Thread(new MyThread1(), "AA").start();

        // Callable接口，报错
        // new Thread(new MyThread2(), "BB").start();

        FutureTask<Integer> futureTask1 = new FutureTask<>(new MyThread2());

        //lambda表达式
        FutureTask<Integer> futureTask2 = new FutureTask<>(()->{
            System.out.println(Thread.currentThread().getName()+" come in callable");
            return 1024;
        });

        new Thread(futureTask2, "lucy").start();

        while(!futureTask2.isDone()) {
            System.out.println("wait...");
        }

        System.out.println(futureTask2.get());

        System.out.println(Thread.currentThread().getName()+" come over");
    }
}
```



# 8.JUC强大的辅助类

## 8.1 减少计数CountDownLatch

CountDownLatch类可以设置一个计数器，然后通过countDown方法来进行减1操作，使用await方法等待计数器不大于0，然后继续执行await方法之后的语句。

+ CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞。
+ 其他线程调用countDown方法会将计数器减1（调用countDown方法的线程不会阻塞）
+ 当计数器的值变为0时，因await方法阻塞的线程会被唤醒，继续执行。

```java
// 演示CountDownLatch
public class CountDownLatchDemo {

    public static void main(String[] args) throws InterruptedException {
        //创建CountDownLatch对象，设置初始值
        CountDownLatch countDownLatch = new CountDownLatch(6);

        // 6个同学陆续离开教室之后，班长锁门
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+" 号同学离开了教室");

                //计数-1
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        //等待
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName()+" 班长锁门");
    }
}
```

## 8.2 循环栅栏CyclicBarrier

允许一组线程全部等待彼此达到共同屏障点的同步辅助。循环阻塞在涉及固定大小的线程方的程序中很有用，这些线程必须偶尔等待彼此。 屏障被称为*循环* ，因为它可以在等待的线程被释放之后重新使用。

CyclicBarrier的构造方法第一个参数是目标障碍数，每次执行CyclicBarrier一次障碍数会加一，如果达到了目标障碍数，才会执行cyclicBarrier.await()之后的语句。

```java
public class CyclicBarrierDemo {

    private static final int NUMBER = 7;

    public static void main(String[] args) {
        //创建CyclicBarrier
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUMBER, ()->{
            System.out.println("集齐7颗龙珠可以召唤神龙");
        });

        //集齐七颗龙珠过程
        for (int i = 1; i <= 7; i++) {
            new Thread(()->{

                try {
                    System.out.println(Thread.currentThread().getName()+" 星龙珠被收集到了");
                    //等待
                    cyclicBarrier.await();
                } catch (Exception e){
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

## 8.3 信号灯Semaphore

```java
//6辆汽车，停3个车位
public class SemaphoreDemo {
    public static void main(String[] args) {
        //创建Semaphore，设置许可数量
        Semaphore semaphore = new Semaphore(3);

        //模拟6辆汽车
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                //抢占
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+" 抢到了车位");

                    //设置随机停车时间
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));

                    System.out.println(Thread.currentThread().getName()+" ----离开了车位");
                } catch (InterruptedException e){
                    e.printStackTrace();
                } finally {
                    //释放
                    semaphore.release();
                }
            }, String.valueOf(i)).start();
        }

    }
}
```

# 9.ReentrantReadWriteLock读写锁

共享锁(S锁)又称读锁，若事务T对数据对象A加上S锁，则事务T可以读A但不能修改A，其他事务只能再对A加S锁，而不能加X锁，直到T释放A上的S 锁。这保证了其他事务可以读A，但在T释放A上的S锁之前不能对A做任何修改。

排他锁(X锁)又称写锁。若事务T对数据对象A加上X锁，事务T可以读A也可以修改A，其他事务不能再对A加任何锁，直到T释放A上的锁。这保证了其他事务在T释放A上的锁之前不能再读取和修改A。

```java
//资源类
class MyCache {

    //创建map集合
    private volatile Map<String, Object> map = new HashMap<>();

    private ReadWriteLock rwLock = new ReentrantReadWriteLock();

    //放数据
    public void put(String key, Object value) {
        //添加写锁
        rwLock.writeLock().lock();

        //暂停一会
        try {
            System.out.println(Thread.currentThread().getName()+" 正在写"+key);

            TimeUnit.MICROSECONDS.sleep(300);
            //放数据
            map.put(key, value);
            System.out.println(Thread.currentThread().getName()+" 写完了"+key);
        } catch (InterruptedException e){
            e.printStackTrace();
        } finally {
            //释放写锁
            rwLock.writeLock().unlock();
        }
    }

    //取数据
    public Object get(String key){
        //添加读锁
        rwLock.readLock().lock();
        Object result = null;
        try {
            System.out.println(Thread.currentThread().getName()+" 正在读取"+key);

            TimeUnit.MICROSECONDS.sleep(300);
            result = map.get(key);
            System.out.println(Thread.currentThread().getName()+" 取完了"+key);

        } catch (InterruptedException e){
            e.printStackTrace();
        } finally {
            //释放读锁
            rwLock.readLock().unlock();
        }

        return result;
    }

}

public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        //创建线程放数据
        for (int i = 1; i <= 5; i++) {
            final int num = i;
            new Thread(()->{
                myCache.put(num+"",num+"");
            },String.valueOf(i)).start();
        }

        //创建线程取数据
        for (int i = 1; i <= 5; i++) {
            final int num = i;
            new Thread(()->{
                myCache.get(num+"");
            },String.valueOf(i)).start();
        }
    }
}
```

写锁可以降级为读锁

+ 获取写锁-->获取读锁-->释放写锁-->释放读锁

读锁不能升级为写锁

# 10.BlockingQueue阻塞队列

在多线程领域，所谓阻塞，在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤醒。

使用BlockingQueue的好处是不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，BlockingQueue会处理一切。

## 10.1 阻塞队列的分类

### 10.1.1 ArrayBlockingQueue(常用)

由数组结构组成的有界阻塞队列

### 10.1.2 LinkedBlockingQueue(常用)

由链表结构组成的有界（大小默认值为integer.MAX_VALUE）阻塞队列

### 10.1.3 DelayQueue

使用优先级队列实现的延迟无界阻塞队列

### 10.1.4 PriorityBlockingQueue

支持优先级排序的无界阻塞队列

### 10.1.5 SynchronousQueue

不存储元素的阻塞队列，也即单个元素的队列

### 10.1.6 LinkedTransferQueue

由链表组成的无界阻塞队列

### 10.1.7 LinkedBlockingDeque

由链表组成的双向阻塞队列

## 10.2 BlockingQueue核心方法

| 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时               |
| -------- | --------- | -------- | ------ | ------------------ |
| 插入     | add(e)    | offer(e) | put(e) | offer(e,time,unit) |
| 移除     | remove()  | poll()   | take() | poll(time,unit)    |
| 检查     | element() | peek()   | 不可用 | 不可用             |

抛出异常：

+ 当阻塞队列满时，再往队列里add插入元素会抛`IllegalStateException:Queue full`
+ 当阻塞队列空时，再往队列里remove移除原色会抛`NoSuchElementException`

特殊值：

+ 插入方法，成功返回true，失败返回false。
+ 移除方法，成功返回出队列的元素，队列里没有就返回null。

阻塞：

+ 当阻塞队列满时，生产者线程继续往队列里put元素，队列会一直阻塞生产者线程直到put数据或响应中断退出。
+ 当阻塞队列空时，消费者线程试图从队列里take元素，队列会一直阻塞消费者直到队列可用。

超时退出：

+ 当阻塞队列满/空时，队列会阻塞生产者/消费者线程一定时间，超时后生产者/消费者线程会退出。

# 11.ThreadPool线程池

**线程池（Thread Pool）**：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务，避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能保证内核的充分利用，还能防止过分调度。

**线程池的优势**：线程池做的工作只是控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排对等候，等其他线程执行完毕，再从队列中取出任务来执行。

其主要特点：

+ 降低资源消耗：通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
+ 提高响应速度：当任务到达时，任务可以不需要等待创建就能立即执行。
+ 提高线程的可管理性：线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一地分配、调优和监控。

Java中的线程池是通过Executor框架实现的，该框架中用到了Executor, Executors, ExecutorService, ThreadPoolExecutor这几个类。

**ThreadPoolExecutor的七个参数**

int corePoolSize	常驻线程数量

int maximumPoolSize	最大线程数量

long keepAliveTime	线程存活时间

TimeUnit unit	线程存活时间

BlockingQueue<Runnable> workQueue	阻塞队列

ThreadFactory threadFactory	线程工厂

RejectedExecutionHandler handler	拒绝策略

**拒绝策略**：

```java
private static final ThreadPoolExecutor THREAD_POOL_EXECUTOR = new ThreadPoolExecutor(4, 20, 60,
            TimeUnit.SECONDS, new ArrayBlockingQueue<>(1000), Executors.defaultThreadFactory(), new ThreadPoolExecutor.AbortPolicy());
```

+ AbortPolicy(默认)：直接抛出RejectedExecutionException异常阻止系统正常运行。
+ CallerRunsPolicy：“调用者运行”一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。
+ DiscardOldestPolicy：抛弃队列中等待最久的任务，然后把当前任务加入队列中，尝试再次提交当前任务。
+ DiscardPolicy：该策略默默地丢弃无法处理的任务，不予任何处理也不抛出异常，如果允许任务丢失，这是最好的一种策略。

> 实际开发中不会使用Executors创建线程池，而是通过ThreadPoolExecutor的方式，这样的处理方式让写代码的人员更加明确线程池的运行规则，避免资源耗尽的风险。

说明：Executors返回的线程池对象的弊端如下：

1）FixedThreadPool和SingleThreadPool:

允许的请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM。

2）CacheThreadPool和ScheduledThreadPool:

允许的创建线程数量为Integer.MAX_VALUE，可能会创建大量的线程，从而导致OOM。

**自定义线程池**

```java
public class ThreadPoolDemo2 {
    public static void main(String[] args) {
        ExecutorService threadPool = new ThreadPoolExecutor(
                2,
                5,
                2L,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );

        try {
            for (int i = 1; i <= 10; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+ " 办理业务");
                });
            }
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }
}
```

# 12.Fork/Join分支合并框架

Fork/Join可以将一个大的任务拆分成多个子任务进行并行处理，最后将子任务结果合并成最后的计算结果，并进行输出。Fork/Join框架要完成两件事情：

+ Fork：把一个复杂任务进行分拆，大事化小
+ Join：把分拆任务的结果进行合并

```java
class MyTask extends RecursiveTask<Integer>{

    private static final Integer VALUE = 10;
    private int begin;//拆分开始值
    private int end;//拆分结束值
    private int result;//返回结果

    //有参构造
    public MyTask(int begin, int end){
        this.begin = begin;
        this.end = end;
    }


    @Override
    protected Integer compute() {
        //判断
        if(end - begin <= VALUE){
            for (int i = begin; i<=end;i++){
                result = result + i;
            }
        } else {
            int middle = (begin + end)/2;
            MyTask task1 = new MyTask(begin,middle);
            MyTask task2 = new MyTask(middle+1,end);
            task1.fork();
            task2.fork();
            //合并结果
            result = task1.join()+task2.join();
        }
        return result;
    }
}
public class ForkJoinDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //创建MyTask对象
        MyTask myTask = new MyTask(0,100);
        //创建分支合并池对象
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Integer> forkJoinTask = forkJoinPool.submit(myTask);

        Integer result = forkJoinTask.get();
        System.out.println(result);
        //关闭池对象
        forkJoinPool.shutdown();
    }
}
```

# 13.CompletableFuture异步回调

```java
public class CompletableFutureDemo {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //异步调用，没有返回值
        CompletableFuture<Void> completableFuture1 = CompletableFuture.runAsync(()->{
            System.out.println(Thread.currentThread().getName()+" completableFuture1");
        });
        completableFuture1.get();

        //异步调用，有返回值
        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+" completableFuture2");
            return 1024;
        });
        completableFuture2.whenComplete((t,u)->{
            System.out.println("t="+t);
            System.out.println("u="+u);
        }).get();
    }
}
```





