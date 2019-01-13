---
title: Java 并发编程
date: 2018-11-18 10:25:41
tags: [Java多线程,笔记整理,面经]
categories: [Java 学习]
---

##### Java 并发基础

在并发编程中，如果想要一个程序正确的执行，必须保证**原子性**、**可见性**以及**有序性**，只要有一个没有被保证，就有可能会导致程序运行不正确。

需要考虑的问题

- 原子问题

  一个操作或多个操作要么全部执行并且执行过程中不能被任何操作打断，要么都不执行。

- 可见性问题

  多个线程访问同一个变量时，一个线程修改了这个变量的值，其它线程能够立即看到修改的值。

- 有序性问题

  程序执行的顺序按照代码的先后顺序执行。在 java 内存模型中，允许编译器和处理器对执行进行重排，但是重排过程不会影响单线程程序的执行，却会影响到多线程并发执行的正确性。

<!-- more -->

**指令重排**

一般来说，处理器为了提高程序运行效率，可能会对输入代码进行优化，它不保证程序中各个语句的执行顺序同代码中的顺序一致，但是它会保证程序最终执行结果和代码顺序执行的结果是一致的。如果两个语句之间没有数据依赖，那么可能会被重排。指令重排不会影响单个线程的执行，但是会影响到线程并发执行的正确性。



##### CyclicBarrier 和 CountDownLatch 的区别

- CountDownLatch 简单的说就是**一个或多个线程等待**，直到它所等待的其他线程都**执行完成**并且调用countDown() 方法发出通知后，当前线程才可以继续执行。
- CyclicBarrier 是**所有线程都进行等待**，直到所有线程**都准备好**进入 await() 方法之后，**所有线程同时开始执行**！
- CountDownLatch 的计数器只能使用一次。而 CyclicBarrier 的计数器可以使用 reset() 方法重置。所以 CyclicBarrier 能处理更为复杂的业务场景，比如如果计算发生错误，可以重置计数器，并让线程们重新执行一次。
- CyclicBarrier 还提供其他有用的方法，比如 getNumberWaiting 方法可以获得 CyclicBarrier 阻塞的线程数量。isBroken 方法用来知道阻塞的线程是否被中断。如果被中断返回true，否则返回false。



##### BIO、NIO、AIO

**同步、异步、阻塞、非阻塞** <https://blog.csdn.net/u013851082/article/details/53942947>

- 同步指的是用户进程触发IO操作并等待或者轮询的去查看IO操作是否就绪

- 异步是指用户进程触发IO操作以后便开始做自己的事情，而当IO操作已经完成的时候会得到IO完成的通知（异步的特点就是通知）

- 阻塞方式是指, 当试图对该文件描述符进行读写时, 如果当时没有东西可读,或者暂时不可写, 程序就进入等待状态, 直到有东西可读或者可写为止

- 非阻塞状态下, 如果没有东西可读, 或者不可写, 读写函数马上返回, 而不会等待

  

**Java对 BIO、NIO、AIO 的支持**

- Java BIO ：同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。
- Java NIO ：同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
- Java AIO ：异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由 OS 先完成了再通知服务器应用去启动线程进行处理数据即可

BIO、NIO、AIO**适用场景**分析:

- BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序直观简单易理解。
- NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。
- AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。



##### ThreadLocal (线程本地存储) <http://www.jasongj.com/java/threadlocal/>

线程的局部变量， 是每一个线程所单独持有的，其他线程不能对其进行访问，可以用来解决对某一个变量的访问冲突问题。当使用 ThreadLocal 维护变量的时候，为每一个使用该变量的线程提供一个独立的变量副本。（会导致内存资源占用增加）



**方法详解**

```java
public T get() { } // 用来获取ThreadLocal在当前线程中保存的变量副本
public void set(T value) { } //set()用来设置当前线程中变量的副本
public void remove() { } //remove()用来移除当前线程中变量的副本
protected T initialValue() { } //initialValue()是一个protected方法，一般是用来在使用时进行重写的
```



**原理**

​	Thread 在内部是通过 ThreadLocalMap 来维护 ThreadLocal 变量表
`ThreadLocal.ThreadLocalMap<ThreadLocal, Object>;`初始化时，在 Thread 里面，threadLocals 为空，当通过 ThreadLocal 变量调用 get() 方法或者 set() 方法，就会对 Thread 类中的 threadLocals 进行初始化，并且以当前 ThreadLocal 变量为键值，以 ThreadLocal 要保存的副本变量为 value，存到 threadLocals。然后在当前线程里面，如果要使用副本变量，就可以通过 get 方法在 threadLocals 里面查找调用ThreadLocal.get方法时，实际上是从当前线程中获取ThreadLocalMap<ThreadLocal, Object>，然后根据当前 ThreadLocal 获取当前线程共享变量Object。



**应用场景**

​	解决 **数据库连接、Session管理** 等。ThreadLocal 适用于每个线程需要自己独立的实例且该实例需要在多个方法中被使用，也即变量在**线程间隔离**而在**方法或类间共享**的场景。



**存储结构的优势**

- ThreadLocalMap 依赖于 Thread ，线程死去的时候，线程本地变量 ThreadLocalMap 则销毁。

- ThreadLocalMap<ThreadLocal,Object>键值对数量为 ThreadLocal 的数量，一般来说 ThreadLocal 数量很少，相比在 ThreadLocal 中用 Map<Thread, Object> 键值对存储线程本地变量（Thread 数量一般来说比 ThreadLocal 数量多），性能提高很多。

  

**ThreadLocalMap 与内存泄漏**

- ThreadLocalMap 的每个 Entry 都是一个对 **键** 的弱引用（下一次GC时会被回收），每个 Entry 都包含了一个对 **值** 的强引用。
- 使用**弱引用的原因**在于，当没有强引用指向 ThreadLocal 变量时（线程未结束，但是引用 ThreadLocal 的对象被回收），它可被回收，从而避免上文所述 ThreadLocal 不能被回收而造成的内存泄漏的问题。但是会导致 ThreadLocal 被回收但是 **值** 未被回收，另外 Entry 无法被移除。从而使得实例被该 Entry 引用而无法被回收造成内存泄漏。
- 针对该问题，ThreadLocalMap 的 set 方法中，通过 **replaceStaleEntry**方法将所有键为 null 的 Entry 的值设置为 null，从而使得该值可被回收。另外，会在 rehash 方法中通过 **expungeStaleEntry** 方法将键和值为 null 的 Entry 设为 null 从而使得该 Entry 可被回收。通过这种方式，ThreadLocal 可防止内存泄漏。

**为什么要使用 ThreadLocal** <https://blog.csdn.net/zhangzeyuaaa/article/details/43564471>

​	如果我们在方法中要使用方法外的变量（不包括当前类或者父类的成员变量），有两种方式可以引用到方法外的变量：

​	1.方法传参。

​	2.将需要被引用的变量定义为类的静态变量。

​	两种方式都有弊端：方法传参的弊端是可能需要在很多地方传递这个参数，耦合度高（因为变量的存储和获取可能在不同的模块中）；定义为类的静态变量则会引发线程安全问题。



**关于ThreadLocalMap内部类的简单介绍**

​	初始容量16，负载因子2/3，解决冲突的方法是**线性探测再散列**（取下一个可用的地址），也就是：在当前hash的基础上再自增一个常量。



##### AQS 抽象队列同步器

​	一套用于实现基于FIFO等待队列的阻塞锁和相关的同步器的一个同步框架。

###### 原理

1. 使用一个 volatile int 类型的成员变量 state 来表示**状态信息**。

- 当 state>0 时表示已经获取了锁，当 state = 0 时表示释放了锁。

- 它提供了三个方法（getState()、setState(int newState)、compareAndSetState(int expect,int update)）来对同步状态 state 进行操作，当然 AQS 可以确保对 state 的操作是安全的。

  比如：Semaphore 用它来表现剩余的许可数，ReentrantLock 用它来表现拥有它的线程已经请求了多少次锁；FutureTask 用它来表现任务的状态(尚未开始、运行、完成和取消)

2. 阻塞线程节点队列 CLH Node queue

   内置的FIFO同步队列来完成资源获取线程的排队工作，如果当前线程获取同步状态失败（锁）时，AQS 则会将当前线程以及等待状态等信息构造成一个节点（Node）并将其加入同步队列，同时会阻塞当前线程，当同步状态释放时，则会把节点中的线程唤醒，使其再次尝试获取同步状态。（阻塞和唤醒使用的是 LockSupport.park()  和  LockSupport.unpark() 阻塞原语）

   CLH 队列是一个非阻塞的 FIFO 队列，也就是说往里面插入或移除一个节点的时候，在并发条件下不会阻塞，而是通过自旋锁和 CAS 保证节点插入和移除的原子性。实现无锁且快速的插入。

   

###### 使用方法 <https://blog.csdn.net/vernonzheng/article/details/8275624>

使用 AQS 来实现一个同步器需要覆盖实现如下几个方法

- tryAcquire(int arg)：独占式获取同步状态，获取同步状态成功后，其他线程需要等待该线程释放同步状态才能获取同步状态；

- tryRelease(int arg)：独占式释放同步状态；

- tryAcquireShared(int arg)：共享式获取同步状态，返回值大于等于0则表示获取成功，否则获取失败；

- tryReleaseShared(int arg)：共享式释放同步状态；

- isHeldExclusively()：当前同步器是否在独占式模式下被线程占用，一般该方法表示是否被当前线程所独占；

  

###### AQS 实现类

继承关系图

![img](https://mubu.com/document_image/a8bb1b96-b524-4032-870a-c491a203fd26-983181.jpg)



*ReentrantLock*

​	需要记录当前线程获取原子状态的次数，如果次数为零，那么就说明这个线程放弃了锁（也有可能其他线程占据着锁从而需要等待），如果次数大于1，也就是获得了重进入的效果，而其他线程只能被park住，直到这个线程重进入锁次数变成0而释放原子状态。

​	内部Sync类实现的是tryAcquire,tryRelease, isHeldExclusively三个方法(因为获取锁的公平性问题，tryAcquire由继承该Sync类的内部类FairSync和NonfairSync实现)

*Semaphore*

​	记录当前还有多少次许可可以使用，到0，就需要等待，也就实现并发量的控制，可作为流量控制。Semaphore一开始设置许可数为1，就相当于一把互斥锁。

​	Semaphore内部类Sync则实现了 tryAcquireShared 和 tryReleasedShared (与 CountDownLatch 相似，因为公平性问题，tryAcquireShared 由其内部类FairSync和NonfairSync实现)。

*CountDownLatch*

​	闭锁则要保持其状态，在这个状态到达终止态之前，所有线程都会被park住，闭锁可以设定初始值，这个值的含义就是这个闭锁需要被countDown()几次，因为每次CountDown是sync.releaseShared(1),而一开始初始值为10的话，那么这个闭锁需要被countDown()十次，才能够将这个初始值减到0，从而释放原子状态，让等待的所有线程通过。

​	CountDownLatch 内部类Sync实现了 tryAcquireShared 和 tryReleasedShared。

*FutureTask*

​	需要记录任务的执行状态，当调用其实例的 get 方法时,内部类 Sync 会去调用AQS的 acquireSharedInterruptibly() 方法，而这个方法会反向调用 Sync 实现的 tryAcquireShared() 方法，即让具体实现类决定是否让当前线程继续还是 park ,而 FutureTask 的 tryAcquireShared 方法所做的唯一事情就是检查状态，如果是 RUNNING 状态那么让当前线程 park 。而跑任务的线程会在任务结束时调用FutureTask 实例的 set 方法（与等待线程持相同的实例），设定执行结果，并且通过 unpark 唤醒正在等待的线程，返回结果。

​	FutureTask内部类 Sync 也实现了 tryAcquireShared 和 tryReleasedShared。



##### java 并发包(J.U.C) 同步控制工具 <https://blog.csdn.net/heting717/article/details/76768971>

###### ReentrantLock

*特性*

- 可重入（获取锁的线程可以重复进入，但要重复退出）

- 可中断 lockInterruptibly()

- 可限时 tryLock()（规定的时间内未能获取锁，则返回 false）

- 公平锁与非公平锁（默认）

  

  一般意义上的锁是不公平的，不一定先来的线程能先得到锁，后来的线程就后得到锁。不公平的锁可能会产生**饥饿现象**。公平锁的意思就是，这个锁能保证线程是先来的先得到锁。虽然公平锁不会产生饥饿现象，但是公平锁的性能会比非公平锁差很多。

*ReentrantLock 的实现*

- 实现主要由3部分组成：
  - CAS状态
  - 等待队列
  - park()
- ReentrantLock 的父类中会有一个 state 变量来表示同步的状态
- 通过 CAS 操作来设置 state 来获取锁，如果设置成了1，则将锁的持有者给当前线程
- 如果拿锁不成功，则会做一个申请，tryAcquire，因为此时可能另一个线程已经释放了锁。
- 如果还是没有申请到锁，就 addWaiter，意思是把自己加到等待队列中去



###### Condition

- Condition与ReentrantLock的关系就类似于 Object.wait()/notify() 与synchronized
- await()方法会使当前线程等待，同时释放当前锁，当其他线程中使用signal()时或者signalAll()方法时，线程会重新获得锁并继续执行。或者当线程被中断时，也能跳出等待。这和 Object.wait() 方法很相似。
- awaitUninterruptibly() 方法与 await() 方法基本相同，但是它并不会在等待过程中响应中断。
- singal() 方法用于唤醒一个在等待中的线程。相对的 singalAll() 方法会唤醒所有在等待中的线程。这和 Obejct.notify() 方法很类似。



###### Semaphore

信号量，控制线程同时进入临界区的数量，可用作流量控制

常用方法:

> acquire() : 获得许可从这个信号量,会阻塞,直到一个可用,或 interrupted线程。
>
> acquireUninterruptibly() : 获得许可从这个信号量,阻塞,直到一个是可用的。
>
> tryAcquire() : 获得许可从这个信号量,只有一个可用的时候调用。
>
> tryAcquire(long timeout, TimeUnit unit) : 获得许可从这个信号量,如果没有一个可用在给定的等待时间和当前线程则返回false,同时可以响应中断。
>
> release() : 发布许可证,返回信号量。



###### ReadWriteLock

读和写是两种不同的功能，读-读不互斥，读-写互斥，写-写互斥。这样的设计是并发量提高了，又保证了数据安全。



###### CountDownLatch 倒数计时器、闭锁

一种典型的场景就是火箭发射。在火箭发射前，为了保证万无一失，往往还要进行各项设备、仪器的检查。只有等所有检查完毕后，引擎才能点火。这种场景就非常适合使用CountDownLatch。它可以使得点火线程，等待所有检查线程全部完工后，再执行。



###### CyclicBarrier 屏障

CyclicBarrier 是所有线程都进行等待，直到所有线程都准备好进入 await() 方法之后，所有线程同时开始执行！另外 CyclicBarrier 可以反复使用。



###### LockSupport

提供线程阻塞原语，和 suspend 类似。与 suspend 相比 不容易引起线程冻结，使用 LockSupport 则不会发生死锁。park() 能够响应中断，但不抛出异常。中断响应的结果是，park() 函数的返回，可以从Thread.interrupted() 得到中断标志。LockSupport 提供 park() 和 unpark() 方法实现**阻塞线程和解除线程阻塞**,实现的阻塞和解除阻塞是基于”许可(permit)”作为关联,permit 相当于一个信号量(0,1)，默认是0。线程之间不再需要一个Object或者其它变量来存储状态,不再需要关心对方的状态。

