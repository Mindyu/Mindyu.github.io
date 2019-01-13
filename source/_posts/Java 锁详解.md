---
title: Java 锁详解
date: 2018-11-16 10:25:41
tags: [synchronized优化,笔记整理,面经]
categories: [Java 学习]
---



##### synchronized 与 Lock 的区别

**synchronized**

- 存在层次：Java 的关键字，在jvm层面上
- 锁的释放：1、获取锁的线程执行完同步代码，释放锁 2、线程执行发生异常，jvm会让线程释放锁
- 锁的获取：假设A线程获得锁，B线程等待。如果A线程阻塞，B线程会一直等待
- 锁状态：无法判断
- 锁类型：可重入 不可中断 非公平
- 性能：适用于少量同步

**Lock**

- 存在层次：一个类

- 锁的释放：在 finally 语句块中必须释放锁，不然容易造成线程死锁

- 锁的获取：分情况而定，Lock 有多个锁获取的方式，可以尝试获得锁，线程可以不会一直等待

- 锁状态：可以判断

- 锁类型：可重入 可中断 可公平（默认为非公平）

- 性能：适用于大量同步

<!-- more -->

**常见问题**：*Synchronized 同步静态方法 和 非静态方法*

- Synchronized 同步静态方法 是对**整个类**加锁
- Synchronized 同步非静态方法 是**对类的某个对象**加锁
- Synchronized(this) 对**当前对象**进行加锁，Synchronized(Test.class)对**类对象**进行加锁

*静态方法同步与对象方法同步不互斥*

```java
/**
测试结果：
Sun Aug 12 11:22:30 CST 2018
同步块完成:Sun Aug 12 11:22:33 CST 2018
同步方法完成:Sun Aug 12 11:22:36 CST 2018
静态方法同步完成:Sun Aug 12 11:22:40 CST 2018
*/
import java.util.Date;
public class Main {
    public static void main(String[] args) {
        Test t = new Test();
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                Test.fun1();        // 静态方法同步，对类进行加锁
            }
        });
        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                t.fun2();            // 对同步块，对Test的对象进行加锁
            }
        });
        
        Thread t3 = new Thread(new Runnable() {
            @Override
            public void run() {
                t.fun3();            // 对非静态方法同步，对Test的对象进行加锁
            }
        });
        System.out.println(new Date());
        t1.start();
        t2.start();
        t3.start();
        
        try {
            t1.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
class Test {
    public synchronized static void fun1() {
        try {
            Thread.sleep(10000);
            System.out.println("静态方法同步完成:"+new Date());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    public void fun2() {
        synchronized (this) {
            try {
                Thread.sleep(3000);
                System.out.println("同步块完成:"+new Date());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    
    public synchronized void fun3() {
        try {
            Thread.sleep(3000);
            System.out.println("同步方法完成:"+new Date());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```



##### synchronized 锁优化 <https://blog.csdn.net/championhengyi/article/details/80105718>

**自旋锁**

- 互斥同步的进入阻塞状态的开销都很大，应该尽量避免。在许多应用中，共享数据的锁定状态只会持续很短的一段时间。自旋锁的思想是让一个线程在请求一个共享数据的锁时执行忙循环（自旋）一段时间，如果在这段时间内能获得锁，就可以避免进入阻塞状态。
- 自旋锁虽然能避免进入阻塞状态从而减少开销，但是它需要进行**忙循环**操作占用 CPU 时间，它只适用于**共享数据的锁定状态很短的场景**。
- 在 JDK 1.6 中引入了自适应的自旋锁。自适应意味着自旋的次数不再固定了，而是由前一次在同一个锁上的自旋次数及锁的拥有者的状态来决定。



**锁消除**

- 锁消除是指对于被检测出不可能存在竞争的共享数据的锁进行消除。

- 锁消除主要是通过**逃逸分析**来支持，如果堆上的共享数据不可能逃逸出去被其它线程访问到，那么就可以把它们当成私有数据对待，也就可以将它们的锁进行消除。

  ```java
  public String concatString(String s1, String s2, String s3) {
      StringBuffer sb = new StringBuffer();    // StringBuffer类是一个线程安全类 
      sb.append(s1);
      sb.append(s2);
      sb.append(s3);
      return sb.toString();
  }
  ```

  

**锁粗化**

- 如果一系列的连续操作都对同一个对象反复加锁和解锁，频繁的加锁操作就会导致性能损耗。



**轻量级锁**

- 相对于传统的重量级锁而言，它使用 CAS 操作来避免重量级锁使用互斥量的开销。**对于绝大部分的锁，在整个同步周期内都是不存在竞争的**，因此也就不需要都使用互斥量进行同步，可以先采用 CAS 操作进行同步，如果 CAS 失败了再改用互斥量进行同步。



**偏向锁**

- 偏向于让第一个获取锁对象的线程，这个线程在之后获取该锁就不再需要进行同步操作，甚至连 CAS 操作也不再需要。
- 当锁对象第一次被线程获得的时候，进入偏向状态，标记为 1 01。同时使用 CAS 操作将线程 ID 记录到 Mark Word 中，如果 CAS 操作成功，这个线程以后每次进入这个锁相关的同步块就不需要再进行任何同步操作。
- 当有另外一个线程去尝试获取这个锁对象时，偏向状态就宣告结束，此时撤销偏向（Revoke Bias）后恢复到未锁定状态或者轻量级锁状态。



##### Java 对象头信息

Mark Word 被设计成为一个非固定的数据结构，以便存储更多有效的数据，它会根据对象本身的状态复用自己的存储空间。 一个字节 32 位。

![img](https://mubu.com/document_image/d8afe36d-7bc3-4f58-b625-aa943c59fdf1-983181.jpg)



##### 锁机制存在的问题

- 在多线程竞争下，加锁、释放锁会导致比较多的上下文切换和调度延时，引起性能问题。
- 一个线程持有锁会导致其它所有需要此锁的线程挂起。
- 如果一个优先级高的线程等待一个优先级低的线程释放锁会导致优先级倒置，引起性能风险。



##### 乐观锁与悲观锁

- 独占锁是一种**悲观锁**，synchronized 就是一种独占锁，会导致其它所有需要锁的线程挂起，等待持有锁的线程释放锁。
- 另一个更加有效的锁就是**乐观锁**。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因冲突而失败就重试，直到成功为止。**乐观锁用到的机制就是CAS，Compare and Swap**。



##### CAS机制（Compare and Swap）

- CAS 操作包含三个操作数 —— **内存位值（V）、预期原值（A）和新值(B)**。 

- 如果内存位置的值与预期原值相匹配，那么处理器会自动将该位置值更新为新值 。否则，处理器不做任何操作。

- 无论哪种情况，它都会在 CAS 指令之前返回该位置的值。（在 CAS 的一些特殊情况下将仅返回 CAS 是否成功，而不提取当前值。）

- 目的： 通过 CAS 机制实现**非阻塞算法**，较与 synchronized 阻塞算法（避免线程间上下文的切换），性能上有很大的提升。

  存在的问题：

  - **ABA问题**。因为 CAS 需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。ABA问题的解决思路就是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A－B－A 就会变成1A－2B－3A。
  - **循环时间长开销大**。自旋CAS如果长时间不成功，会给CPU带来非常大的执行开销。
  - 只能**保证一个共享变量的原子操作**。当对一个共享变量执行操作时，我们可以使用循环 CAS 的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性。解决办法有：使用锁或者将多个变量封装为一个对象 。

- concurrent (J.U.C)包的源代码实现原理

  - 声明共享变量为 volatile
  - 使用 CAS 的原子条件更新来实现线程之间的同步
  - 配合以 volatile 的读/写和 CAS 所具有的 volatile 读和写的内存语义来实现线程之间的通信。



##### 线程同步的多种实现

- Synchronized
- Lock
- Semaphore（1）
- volatile 共享变量



##### 生产者与消费者问题

<https://zhuanlan.zhihu.com/p/20300609>
