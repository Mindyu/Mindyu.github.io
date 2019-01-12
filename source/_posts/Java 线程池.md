---
title: Java 线程与线程池
date: 2018-11-14 15:16:41
tags: [线程池,线程,笔记整理,面经]
categories: [Java 学习]
---



#### Java线程

##### Java线程状态

> 1. 初始(NEW)：新创建线程对象，但还没有调用start()方法。
> 2. 运行(RUNNABLE)：Java 虚拟机中线程的可运行状态包括操作系统中线程的就绪（ready）和运行中（running）两种状态。即处于该状态时，线程可能正在等待来自操作系统的其他资源（如CPU）。
> 3. 阻塞(BLOCKED)：表示线程阻塞于锁。
> 4. 等待(WAITING)：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
> 5. 超时等待(TIMED_WAITING)：该状态不同于WAITING，它可以在指定的时间后自行返回。
> 6. 终止(TERMINATED)：表示该线程已经执行完毕。

<!-- more -->

##### 线程的状态变化图

![img](https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2018/Java%E7%AC%94%E8%AE%B0/%E7%BA%BF%E7%A8%8B%E7%8A%B6%E6%80%81%E5%9B%BE.jpeg)

JDK1.8 线程状态源码

```java
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

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
        WAITING,

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
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }

```



##### 线程常用方法

- thread.join() <https://www.cnblogs.com/huangzejun/p/7908898.html>

  - 作用
    - 当我们调用某个线程的这个方法时，这个方法会挂起调用线程，直到被调用线程结束执行，调用线程才会继续执行。
  - 可以用来实现**多个线程需要按一定的顺序执行**的需求。

- Thread.yield() 线程让步（静态本地方法）

  - 作用
    - 让当前线程由“运行状态”进入到“就绪状态”，从而让其它具有相同优先级的等待线程获取执行权；但是，并不能保证在当前线程调用yield()之后，其它具有相同优先级的线程就一定能获得执行权；也有可能是当前线程又进入到“运行状态”继续运行！

- Thread.sleep() 线程睡眠 （静态本地方法）

  - 作用

    - 让当前线程由“运行状态”进入到“阻塞状态”，并不会释放锁。

    

##### 线程中断方法

- interrupted()
  - 返回当前的线程的中断状态，并清除中断标识位。静态方法
    public static boolean interrupted() {        return currentThread().isInterrupted(true);    }
- isInterrupted()
  - 返回调用该方法的对象所表示的线程的中断状态，且不会清除中断标识位。实例方法
    public boolean isInterrupted() {        return isInterrupted(false);    }

代码测试：

![img](https://mubu.com/document_image/db2709d8-0f5f-458d-bf74-a95fb6f8d466-983181.jpg)



#### 线程池

##### 详细参数

- **corePoolSize**：核心池的大小。在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是**预创建线程**的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；
- **maximumPoolSize**：线程池**最大线程数**，它表示在线程池中最多能创建多少个线程；
- **keepAliveTime**：表示**线程没有任务执行时最多保持多久时间会终止**。默认情况下，当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0；
- unit：参数keepAliveTime的时间单位
- **workQueue**：一个阻塞队列，用来存储等待执行的任务
- threadFactory：线程工厂，主要用来创建线程
- handler：表示当拒绝处理任务时的策略



##### 线程池状态

- runState 表示当前线程池的状态，它是一个 volatile 变量用来保证线程之间的可见性，高3位表示状态。

  ```java
  volatile int runState;
  static final int RUNNING    = 0;
  static final int SHUTDOWN   = 1;
  static final int STOP       = 2;
  static final int TERMINATED = 3;
  ```

- 当创建线程池后，初始时，线程池处于RUNNING状态；

- 如果调用了shutdown()方法，则线程池处于SHUTDOWN状态，此时线程池不接受新任务，但处理排队任务

- 如果调用了shutdownNow()方法，则线程池处于STOP状态，此时线程池不接受新任务，不处理排队任务，并中断正在进行的任务

- 当线程池处于SHUTDOWN或STOP状态，并且所有工作线程已经销毁，任务缓存队列已经清空或执行结束后，线程池被设置为TERMINATED状态。

**1.8 使用的原子类**

![img](https://mubu.com/document_image/7935a7c8-e708-4cd0-9220-09d2dc12f767-983181.jpg)



##### 线程池的种类

- fixThreadPool  固定线程
  有指定的线程数的线程池，有核心的线程，里面有固定的线程数量，响应的速度快。正规的并发线程，多用于服务器。固定的线程数由系统资源设置。
- cacheThreadPool  缓存线程池
  只有非核心线程，最大线程数很大（Int.Max(values)），它会为每一个任务添加一个新的线程，这边有一个超时机制，当空闲的线程超过60s内没有用到的话，就会被回收。缺点就是没有考虑到系统的实际内存大小。
- singleThreadPool   单线程线程池
  只有一个核心线程，通过指定的顺序将任务一个个丢到线程，都乖乖的排队等待执行，不处理并发的操作，不会被回收。效率慢。
- ScheduledThreadPool   
  唯一一个有延迟执行和周期重复执行的线程池。它的核心线程池固定，非核心线程的数量没有限制，但是闲置时会立即被回收。

##### 线程池的实现 <https://www.cnblogs.com/wxwall/p/7050698.html>

**原理图**

![img](https://mubu.com/document_image/fbaf90a5-779a-4c70-95a2-f591d7c77276-983181.jpg)

**实现要点**

- 线程池里的核心线程数与最大线程数
- 线程池里真正工作的线程 worker
- 线程池里用来存取任务的队列 BlockingQueue
- 线程中的任务 task

**阻塞队列 BlockingQueue** <http://ifeve.com/java-blocking-queue/>

- 定义

  - 一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。

- 作用

  - 常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。

- 四种处理方法

  - put、take、offer、poll、add、remove 的区别

    ![img](https://mubu.com/document_image/abc70866-0760-4e45-b895-ac1968ac0801-983181.jpg)

  - 区别详解

    - 抛出异常：是指当阻塞队列满时候，再往队列里插入元素，会抛出IllegalStateException(“Queue full”)异常。当队列为空时，从队列里获取元素时会抛出NoSuchElementException异常 。实际上调用的还是返回特殊值的方法。
    - 返回特殊值：插入方法会返回是否成功，成功则返回true。移除方法，则是从队列里拿出一个元素，如果没有则返回null
    - 一直阻塞：当阻塞队列满时，如果生产者线程往队列里put元素，队列会一直阻塞生产者线程，直到拿到数据，或者响应中断退出。当队列空时，消费者线程试图从队列里take元素，队列也会阻塞消费者线程，直到队列可用。
    - 超时退出：当阻塞队列满时，队列会阻塞生产者线程一段时间，如果超过一定的时间，生产者线程就会退出。

    

**Java常用阻塞队列**

- ArrayBlockingQueue

  ArrayBlockingQueue是一个用数组实现的有界阻塞队列。此队列按照先进先出（FIFO）的原则对元素进行排序。默认情况下不保证访问者公平的访问队列，所谓公平访问队列是指阻塞的所有生产者线程或消费者线程，当队列可用时，可以按照阻塞的先后顺序访问队列。通常情况下为了保证公平性会降低吞吐量。底层以数组的形式保存数据(实际上可看作一个循环数组)。在添加元素取出元素时会加锁。

- LinkedBlockingQueue

  一个用链表实现的有界阻塞队列。此队列的默认的最大长度为Integer.MAX_VALUE。此队列按照先进先出的原则对元素进行排序。

- PriorityBlockingQueue

  一个支持优先级的无界队列。默认情况下元素采取自然顺序排列，也可以通过比较器 comparator 来指定元素的排序规则。元素按照升序排列。

- SynchronousQueue

  一个不存储元素的阻塞队列。每一个 put 操作必须等待一个 take 操作，否则不能继续添加元素。SynchronousQueue 可以看成是一个传球手，负责把生产者线程处理的数据直接传递给消费者线程。队列本身并不存储任何元素，非常适合于传递性场景,比如在一个线程中使用的数据，传递给另外一个线程使用，SynchronousQueue的吞吐量高于LinkedBlockingQueue 和 ArrayBlockingQueue。

**任务拒绝策略**

- ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
- ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
- ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
- ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务



##### 线程池的主要处理流程

![img](https://mubu.com/document_image/b260e0bf-9055-419c-b565-6903bc9b1957-983181.jpg)



##### 线程池的好处

- 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- 提高响应速度。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- 提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。