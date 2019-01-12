---
title: Java GC笔记
date: 2018-11-13 10:06:41
tags: [垃圾回收器,GC算法,笔记整理,面经]
categories: [Java 学习]
---



#### GC 算法

**标记清除算法 Mark-Sweep**

- 原理

  - 标记出所有需要回收的对象
  - 标记完成后统一回收所有被标记的对象![img](https://mubu.com/document_image/e4f0c0d5-4045-4f24-b6ae-8ad9bd67b5b8-983181.jpg)

- 缺点

  - 效率不高，标记和清除效率都不高
  - 空间碎片问题，标记清除之后会产生大量不连续的内存碎片,空间碎片太多可能会导致以后在程序运行过程中需要分配较大对象时,无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作

  <!-- more -->

**复制算法 Copying**

- 原理

  - 将可用内存按容量划分为大小相等的两块,每次只使用其中的一块。

  - 当这一块的内存用完了,就将还存活着的对象依次复制到另外一块上面,然后再把已使用过的内存空间一次性清理掉。

    ![img](https://mubu.com/document_image/bb382fc9-43e3-47a3-9ca6-b1c3731dc613-983181.jpg)

- 优点

  - 实现简单，运行高效
  - 不用考虑内存碎片问题

- 缺点

  - 可用内存缩小为原来的一半
  - 在对象存活率较高的情况下，会进行较多的复制操作导致效率降低

- 标记整理算法 Mark-Compact

  - 原理

    - 标记

    - 将所有存活的对象向一端移动，然后清理掉边界以外的内存

      ![img](https://mubu.com/document_image/e70278a1-4648-4b87-9277-68237fb0efae-983181.jpg)

  **分代收集算法 Generational Collection**

  - 原理
    - 根据对象存活周期的不同将内存划分为几块。
    - 一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。
    - 在新生代中,每次垃圾收集时都发现有大批对象死去,只有少量存活,那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。
    - 老年代中因为对象存活率高、没有额外空间对它进行分配担保,就必须使用“标记—清理”或者“标记—整理”算法来进行回收。



#### 垃圾回收器

**Serial 收集器**

- Serial（串行）垃圾收集器是最基本、发展历史最悠久的收集器

- 特点

  - 针对新生代；
  - 采用复制算法；
  - 单线程收集；
  - 进行垃圾收集时，必须暂停所有工作线程，直到完成，即会"Stop The World"；

-  Serial/Serial Old组合收集器运行示意图

  ![img](https://mubu.com/document_image/b5ba06ff-4688-4d46-91e8-64245ee2b45c-983181.jpg)

**ParNew 收集器**

- ParNew 垃圾收集器是 Serial 收集器的多线程版本。

- 特点

  - 新生代收集器；
  - 采用复制算法；
  - 多线程收集；

-  ParNew/Serial Old组合收集器运行示意图

  ![img](https://mubu.com/document_image/316e4e38-9858-4b95-863e-7530388bb69a-983181.jpg)

**Parallel Scavenge 收集器**

- 与吞吐量关系密切，也称为吞吐量收集器（Throughput Collector）吞吐量=运行用户代码时间/（运行用户代码时间+垃圾收集时间）
- 高吞吐量即减少垃圾收集时间，让用户代码获得更长的运行时间
- 特点
  - 新生代收集器；
  - 采用复制算法；
  - 多线程收集；
  - 它的关注点与其他收集器不同
    - CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间；
    - Parallel Scavenge 收集器的目标则是达一个可控制的吞吐量（Throughput）
  - GC自适应的调节策略（GC Ergonomiscs）
    - 只需设置好内存数据大小（如"-Xmx"设置最大堆）；
    - 然后使用"-XX:MaxGCPauseMillis"或"-XX:GCTimeRatio"给JVM设置一个优化目标；
    - 那些具体细节参数的调节就由JVM自适应完成；

**Serial Old 收集器**

- Serial Old 是 Serial 收集器的老年代版本；

- 特点

  - 针对老年代；
  - 采用"标记-整理"算法（还有压缩，Mark-Sweep-Compact）；
  - 单线程收集；

- Serial/Serial Old收集器运行示意图

  ![img](https://mubu.com/document_image/916c2bb2-09dd-4f5d-b59b-d5ac58b1c133-983181.jpg)

- 应用场景

  - 主要用于 Client 模式；
  - 在 Server 模式有两大用途
    - 在JDK1.5及之前，与 Parallel Scavenge 收集器搭配使用（JDK1.6有Parallel Old收集器可搭配）
    - 作为CMS收集器的后备预案，在并发收集发生 Concurrent Mode Failure 时使用

**Parallel Old 收集器**

- Parallel Old 垃圾收集器是 Parallel Scavenge 收集器的老年代版本；JDK1.6中才开始提供

- 特点

  - 针对老年代；
  - 采用"标记-整理"算法；
  - 多线程收集；

-  Parallel Scavenge/Parallel Old 收集器运行示意图

  ![img](https://mubu.com/document_image/cfc359f4-21a0-4f03-a4b5-ae39ac349047-983181.jpg)

- 使用场景

  - JDK1.6及之后用来代替老年代的 Serial Old 收集器；
  - 特别是在Server模式，多CPU的情况下；
  - 这样在注重吞吐量以及CPU资源敏感的场景，就有了 Parallel Scavenge 加 Parallel Old收集器的"给力"应用组合

**CMS收集器**

- 并发标记清理（Concurrent Mark Sweep，CMS）收集器也称为并发低停顿收集器（Concurrent Low Pause Collector）或低延迟（low-latency）垃圾收集器

- 特点

  - 针对老年代；
  - 基于"标记-清除"算法(不进行压缩操作，产生内存碎片)；
  - 以获取最短回收停顿时间为目标；
  - 并发收集、低停顿；
  - 需要更多的内存（缺点）

- 应用场景

  - 与用户交互较多的场景；
  - 希望系统停顿时间最短，注重服务的响应速度；以给用户带来较好的体验；
  - 如常见WEB、B/S系统的服务器上的应用；

- 运行过程

  - 初始标记（CMS initial mark）
    - 仅标记一下GC Roots能直接关联到的对象；
    - 速度很快；
    - 但需要"Stop The World"；
  - 并发标记（CMS concurrent mark）
    - 进行GC Roots Tracing的过程；
    - 刚才产生的集合中标记出存活对象；
    - 应用程序也在运行；
    - 并不能保证可以标记出所有的存活对象；
  - 重新标记（CMS remark）
    - 为了修正并发标记期间因用户程序继续运作而导致标记变动的那一部分对象的标记记录；
    - 需要"Stop The World"，且停顿时间比初始标记稍长，但远比并发标记短；
    - 采用多线程并行执行来提升效率；
  - 并发清除（CMS concurrent sweep）
    - 回收所有的垃圾对象；

- 缺点

  - 对CPU资源非常敏感
    - 并发收集虽然不会暂停用户线程，但因为占用一部分CPU资源，还是会导致应用程序变慢，总吞吐量降低。
    - CMS 的默认收集线程数量是=(CPU数量+3)/4；
  - 无法处理浮动垃圾
    - 在并发清除时，用户线程新产生的垃圾，称为浮动垃圾；
  - 产生大量内存碎片
    - CMS 基于"标记-清除"算法，清除后不进行压缩操作；可通过配置实现内存碎片整理
  - promotion failed – concurrent mode failure
    - Minor GC后， surviver 空间容纳不了剩余对象，将要放入老年代，老年代有碎片或者不能容纳这些对象，即 CMS 预留内存空间无法满足程序需要，就会出现一次"Concurrent Mode Failure"失败。
    - 解决方案
      - 让 CMS 在进行一定次数的 Full GC（标记清除）的时候进行一次标记整理算法，在一定程度上控制碎片的数量。 使用标记整理清除碎片
        -XX:UseCMSCompactAtFullCollection -XX:CMSFullGCBeforeCompaction=5
      - 增大新生代或者surviver区空间大小
  - concurrent mode failure
    - 将大对象放入老年代空间时，老年代空间无法满足程序需要，就会出现一次"Concurrent Mode Failure"失败。这时JVM启用后备预案：临时启用Serail Old收集器，而导致另一次 Full GC 的产生，此时代价较大(会Stop the world)，所以CMS预留内存空间不能设置得太小。
    - 解决方案
      - 调低触发 CMS GC 执行的阀值。CMS GC 触发主要由 CMSInitiatingOccupancyFraction 值决定，默认情况是当老年代已用空间为68%时，即触发 CMS GC。尽早进行CMS操作，使其预留空间变大
      - 增大老年代空间。

-  CMS 收集器运行示意图

  ![img](https://mubu.com/document_image/415ade9c-b92f-40c2-9f51-830bdaaaf757-983181.jpg)

**G1 收集器**

- G1（Garbage-First）是JDK7-u4才推出商用的收集器；

- 特点

  - 并行与并发
  - 分代收集，收集范围包括新生代和老年代   
  - 结合多种垃圾收集算法，空间整合，不产生碎片
  - 可预测的停顿：低停顿的同时实现高吞吐量

- 应用场景

  - 面向服务端应用，针对具有大内存、多处理器的机器；
  - 最主要的应用是为需要低GC延迟，并具有大堆的应用程序提供解决方案；
  - 如：在堆大小约6GB或更大时，可预测的暂停时间可以低于0.5秒；

- 运作过程

  - 初始标记（Initial Marking）

    - 仅标记一下GC Roots能直接关联到的对象；
    - 且修改TAMS（Next Top at Mark Start）,让下一阶段并发运行时，用户程序能在正确可用的 Region 中创建新对象；
    - 需要"Stop The World"，但速度很快；

  - 并发标记（Concurrent Marking）

    - 进行 GC Roots Tracing 的过程；
    - 刚才产生的集合中标记出存活对象；
    - 耗时较长，但应用程序也在运行；
    - 并不能保证可以标记出所有的存活对象；

  - 最终标记（Final Marking）

    - 为了修正并发标记期间因用户程序继续运作而导致标记变动的那一部分对象的标记记录；
    - 上一阶段对象的变化记录在线程的Remembered Set Log；
    - 这里把Remembered Set Log合并到Remembered Set中；
    - 需要"Stop The World"，且停顿时间比初始标记稍长，但远比并发标记短；
    - 采用多线程并行执行来提升效率；

  - 筛选回收（Live Data Counting and Evacuation）

    - 首先排序各个 Region 的回收价值和成本；
    - 然后根据用户期望的GC停顿时间来制定回收计划；
    - 最后按计划回收一些价值高的 Region 中垃圾对象；
    - 回收时采用"复制"算法，从一个或多个Region复制存活对象到堆上的另一个空的Region，并且在此过程中压缩和释放内存；
    - 可以并发进行，降低停顿时间，并增加吞吐量；

  - G1收集器运行示意图

    ![img](https://mubu.com/document_image/ca0c2332-d94f-44b0-bed2-a33fd9910cb5-983181.jpg)

#### 参数配置

- 收集器设置
  - -XX:+UseSerialGC:设置串行收集器
  - -XX:+UseParallelGC:设置并行收集器
  - -XX:+UseParalledlOldGC:设置并行年老代收集器
  - -XX:+UseConcMarkSweepGC:设置并发收集器
- 垃圾回收统计信息
  - -XX:+PrintGC
  - -XX:+PrintGCDetails
  - -XX:+PrintGCTimeStamps
  - -Xloggc:filename
- 并行收集器设置
  - -XX:ParallelGCThreads=n:设置并行收集器收集时使用的CPU数。并行收集线程数。
  - -XX:MaxGCPauseMillis=n:设置并行收集最大暂停时间
  - -XX:GCTimeRatio=n:设置垃圾回收时间占程序运行时间的百分比。公式为1/(1+n)
- 并发收集器设置
  - -XX:+CMSIncrementalMode:设置为增量模式。适用于单CPU情况。
  - -XX:ParallelGCThreads=n:设置并发收集器年轻代收集方式为并行收集时,使用的CPU数。并行收集线程数。

- 垃圾收集器期望的目标
  - 停顿时间
    - 停顿时间越短就适合需要与用户交互的程序；
    - 良好的响应速度能提升用户体验；
  - 吞吐量
    - 高吞吐量则可以高效率地利用CPU时间，尽快完成运算的任务；
    - 主要适合在后台计算而不需要太多交互的任务；
  - 覆盖区
    - 在达到前面两个目标的情况下，尽量减少堆的内存空间；
    - 可以获得更好的空间局部性；



#### 面试常见的问题

- 对象提升原则
  - 对象优先分配在 Eden 区，空间不够时，执行 Minor GC 。
  - 大对象直接进入老年代（需要大量连续内存空间的对象）。目的：避免在 Eden 区和两个 Survivor 区之间发生大量的内存拷贝。
  - 长期存活的对象进入老年代。Survivor 区中的对象每经过一次 Minor GC ，其对象年龄就+1。
  - 动态判断对象的年龄。如果 Survivor 区中的相同年龄的所有对象的总和大于 Survivor 空间的一半，年龄大于或等于该年龄的对象可以直接进入老年代。
  - 空间分配担保。每次 Minor GC 时，计算 Survivor 区移至老年区的对象平均大小，是否大于老年区剩余空间大小。是否需要 Full GC。
- 触发 Full GC 的情况
  - 老年代空间不足
  - 永久代被占满
  - CMS GC时出现 Promotion Failed 和 Concurrent Mode Failure（产生的浮动垃圾）
  - 空间分配担保失败（晋升到老年代的平均大小大于老年代的剩余空间）
- Stop the World
  - GC 事件/过程发生过程当中停止所有应用程序线程的执行。
  - 停顿保证系统状态在某一瞬间的一致性，并且不会产生新的垃圾，有利于垃圾回收器更好的标记垃圾对象。
- 判断对象是否存活
  - 引用计数法 问题： 循环引用问题
  - GC ROOTS 可达行分析法
    - Java 虚拟机栈引用的对象
    - 本地方法栈引用的对象
    - 方法区静态变量引用的对象
    - 方法区常量引用的对象
- **GC是在什么时候，对什么东西，做了什么事情**
  - 堆中年轻代的 eden 区满执行 minor gc，提升到老年代的对象大于老年代剩余空间大小则执行 full gc。我们无法控制 GC 发生时间，另外 System.gc 只是建议 JVM 进行 GC 。
  - 从 gc root 开始搜索找不到的对象，而且经过一次标记、清理，仍然没有复活的对象。
  - 删除不使用的对象，回收内存空间；运行默认的 finalize，JVM 对年轻代执行复制算法，对老年代执行标记清理/标记整理算法。

