---
title: Java 集合笔记
date: 2018-11-12 16:06:41
tags: [Java集合,笔记整理,面经]
categories: [Java 学习]
---


#### Java 集合关系图

![img](https://mubu.com/document_image/1cc193b0-f4d2-4d6f-a8a6-fb5f0f257956-983181.jpg) 

#### Iterator 和 ListIterator 的区别

**Iterator 接口**

- 可以应用于所有的集合，Set、List和Map和这些集合的子类型
- 只能顺序向后遍历.
- 只能遍历，使用迭代器的方法可以删除元素。

**ListIterator**

- 只能用于List及其子类型。
- 有add方法，可以向List中添加对象
- 有hasPrevious()和previous()方法，可以实现逆向（顺序向前）遍历
- 可以定位当前索引的位置，nextIndex()和previousIndex()可以实现。
- 可以实现对象的修改，set()方法可以实现。

<!-- more -->

#### HashSet与TreeSet的区别

**HashSet**

- 内部使用 HashMap 存储对象，将对象存储在 map 的 key 处，而 map 的 value 默认为一个静态的 object 对象。

  ```java
  // Dummy value to associate with an Object in the backing Map
  private static final Object PRESENT = new Object();
  ```

- HashSet 实现了 Set 接口，它不允许集合中出现重复元素。当我们提到HashSet时，第一件事就是在将对象存储在HashSet之前，要确保重写hashCode() 方法和equals() 方法，这样才能比较对象的值是否相等，确保集合中没有储存相同的对象。



**TreeSet**

- 通过 TreeMap 实现，而 TreeMap 基于红黑树实现排序的效果。

  ![img](https://mubu.com/document_image/c0a55a08-5121-4c5c-aeeb-0d04dbad2357-983181.jpg)

- 特点

  - TreeSet是用来排序的, 可以指定一个顺序, 对象存入之后会按照指定的顺序排列

- 使用方式

  - a.自然顺序(Comparable)
    - TreeSet类的add()方法中会把存入的对象提升为Comparable类型
    - 调用对象的compareTo()方法和集合中的对象比较
    - 根据compareTo()方法返回的结果进行存储
  - b.比较器顺序(Comparator)
    - 创建TreeSet的时候可以制定一个Comparator
    - 如果传入了Comparator的子类对象, 那么TreeSet就会按照比较器中的顺序排序
    - add()方法内部会自动调用Comparator接口中compare()方法排序
    - 调用的对象是compare方法的第一个参数,集合中的对象是compare方法的第二个参数
  - c.两种方式的区别
    - TreeSet构造函数什么都不传, 默认按照类中 Comparable 的顺序(没有就报错ClassCastException)
    - TreeSet如果传入Comparator, 就优先按照Comparator



#### ArrayList与LinkedList的区别

**ArrayList**

- 基于动态数组实现
- 支持随机访问
- 不利于插入与删除

**LinkedList**

- 基于链表实现
- 新增与删除操作只需移动指针
- 访问时需要遍历



#### ArrayList与Vector的区别

**ArrayList**

- 默认长度为 10
- 扩容时*1.5，更节省空间

**Vector**

- 方法都是同步的，线程安全
- 默认长度也是 10
- 扩容时*2



#### CopyOnWrite容器（写时复制的容器）

原理：

- 当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。

优势：

- 可以对 CopyOnWrite 容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以 CopyOnWrite 容器也是一种读写分离的思想，读和写不同的容器。

缺点：

- 内存占用问题
  - 因为CopyOnWrite的写时复制机制，所以在进行写操作的时候，内存里会同时驻扎两个对象的内存，旧的对象和新写入的对象。注意:在复制的时候只是复制容器里的引用，只是在写的时候会创建新对象添加到新容器里，而旧容器的对象还在使用，所以有两份对象内存。
- 数据一致性问题
  - CopyOnWrite 容器只能保证数据的最终一致性，不能保证数据的实时一致性。

使用场景：

- 适用于读多写少的并发场景

- CopyOnWriteArrayList

  - 添加的时候是需要加锁的
  - 读的时候不需要加锁，如果读的时候有多个线程正在向CopyOnWriteArrayList添加数据，读还是会读到旧的数据，因为写的时候不会锁住旧的CopyOnWriteArrayList。

  

#### ConcurrentHashMap、Collections.synchronizedMap、HashMap与HashTable的区别

***equals 方法和 hashCode 方法***

- 重写 equals 方法时必须要重写 hashCode 方法
  - 在一个应用程序执行期间，如果一个对象的 equals 方法做比较所用到的信息没有被修改的话，那么对该对象调用 hashCode 方法多次，它必须始终如一地返回同一个整数。在同一个应用程序的多次执行过程中，这个整数可以不同，即这个应用程序这次执行返回的整数与下一次执行返回的整数可以不一致。
  - 如果两个对象根据 equals(Object) 方法是相等的，那么调用这两个对象中任一个对象的 hashCode 方法必须产生同样的整数结果。
  - 如果两个对象根据 equals(Object) 方法是不相等的，那么调用这两个对象中任意一个对象的hashCode方法，不要求必须产生不同的整数结果。

静态类部类 Node<K,V> 重写了 hashCode 方法

```java
public final int hashCode() {       
	return Objects.hashCode(key) ^ Objects.hashCode(value); 
	// 会去调用key和value重写的hashCode 方法，然后异或
}
```
String 的 hashCode `h = 31 * h + val[i];`

- 为什么乘 31
  - 31是一个素数，素数作用就是如果我用一个数字来乘以这个素数，那么最终的出来的结果只能被素数本身和被乘数还有1来整除！。(减少冲突)
  - 31可以 由 i*31== (i<<5)-i 来表示,现在很多虚拟机里面都有做相关优化.（提高算法效率）
  - 选择系数的时候要选择尽量大的系数。因为如果计算出来的hash地址越大，所谓的“冲突”就越少，查找起来效率也会提高。（减少冲突）
  - 31只占用5bits,相乘造成数据溢出的概率较小。

***Hash 冲突的解决方法***

- 开放定址法
  - 线性探测再散列   di = 1 , 2 , 3 , ... , m-1
  - 平方探测再散列   di = 1^2 , -1^2 , 2^2 , -2^2 , 3^2 , -3^2 , ... , k^2 ,  -k^2 (避免聚集)
  - 随机探测再散列   di 是一组伪随机数列

- 链地址法

- 再哈希 （有多个 hashCode 方法）

- 建立公共溢出区

  

  **HashMap**

  - capacity	：当前数组容量，始终保持 2^n，可以扩容，扩容后数组大小为当前的 2 倍。loadFactor：负载因子，默认为 0.75。threshold：扩容的阈值，等于 capacity * loadFactor
  - 继承自 AbstractMap，实现了 Map 接口
  - 线程不安全
  - 只有containsValue和containsKey方法
  - 允许有一个 null 键和多个 null 值
  - 重新计算 hash 值
    `return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16); // 高16位与低16位异或运算`
  - 默认容量为 16 ，扩容时 *2（总是 2 的次方，原因是可以使用位运算取代取模运算）
  - 1.7与1.8的区别
    - 1.7 采用头插法，问题是多线程下扩容可能导致死锁。
    - 1.8 采用尾插法；当链表长度>=8时，采用红黑树，提高检索效率。



**HashTable**	

- 继承自 Dictionary，实现 Map 、Enumeration接口
- 线程安全
- 有containsValue、contains（同containsValue）和containsKey方法
- 键和值都不允许有 null。put(null, null)时，编译可以通过，但是运行时会抛出空指针异常

- 直接使用对象的 hash 值
- 默认容量为 11 ，扩容时 *2+1（保证为奇数）
  为什么保证为奇数？在应用数据分布在等差数据集合(如偶数)上时，如果公差与桶容量有公约数n，则至少有 (n-1)/n 数量的桶是利用不到的。 实际上 HashMap 也会有此问题，并且不能指定桶容量。所以 HashMap 会在取模哈希前先做一次哈希。



**ConcurrentHashMap** <http://www.importnew.com/22007.html>

*JDK 1.7实现*

一个ConcurrentHashMap由多个segment(分段锁)（默认为16个）组成，每个segment包含一个 Entry 的数组。这里比HashMap多了一个segment类。该类继承了ReentrantLock类，所以本身是一个锁。当多线程对ConcurrentHashMap操作时，不是完全锁住map，而是锁住相应的segment。这样提高了并发效率。 

Segment 数组不可以扩容，每个segment中的Entry的数组初始大小为2，可以扩容。

ConcurrentHashMap 提供并发的检索和更新操作，功能规范和 hashtable 一样。

更新操作的并发级别是可以配置的，由 concurrencyLevel 决定，作为hint。这个参数将整个hash表分割成相互独立的子表，无竞争操作。这个 concurrencyLevel 的设置根据使用场景的并发修改量来确定，过大则浪费内存空间，过小则影响并发性能。最好是在构造的时候就确定。

当需要遍历、size()、empty()时，需要获取所有的 segment 的锁，效率降低。

执行 size() 时，先尝试不加锁，如果连续两次不加锁得到的结果一致，则认为这个值是正确的。如果连续执行三次，依然不满足，则对每一个 segment 进行加锁求 size。

不允许空键、空值。同hashtable,异hashmap。

迭代元素不会抛出并发修改异常，迭代元素只是迭代器创建时数据结构的快照。迭代同时只能被一个线程使用。



*JDK 1.8实现*

当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)。

put 的详细过程

```java
  final V putVal(K key, V value, boolean onlyIfAbsent) {
   if (key == null || value == null) throw new NullPointerException();
   // 得到 hash 值
   int hash = spread(key.hashCode());
   // 用于记录相应链表的长度
   int binCount = 0;
   for (Node<K,V>[] tab = table;;) {
       Node<K,V> f; int n, i, fh;
       // 如果数组"空"，进行数组初始化
       if (tab == null || (n = tab.length) == 0)
           // 初始化数组，后面会详细介绍
           tab = initTable();

       // 找该 hash 值对应的数组下标，得到第一个节点 f
       else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
           // 如果数组该位置为空，
           // 用一次 CAS 操作将这个新值放入其中即可，这个 put 操作差不多就结束了，可以拉到最后面了
           // 如果 CAS 失败，那就是有并发操作，进到下一个循环就好了
           if (casTabAt(tab, i, null,new Node<K,V>(hash, key, value, null)))
               break;                   // no lock when adding to empty bin
       }
       // hash 居然可以等于 MOVED，这个需要到后面才能看明白，不过从名字上也能猜到，肯定是因为在扩容
       else if ((fh = f.hash) == MOVED)
           // 帮助数据迁移，这个等到看完数据迁移部分的介绍后，再理解这个就很简单了
           tab = helpTransfer(tab, f);

       else { // 到这里就是说，f 是该位置的头结点，而且不为空

           V oldVal = null;
           // 获取数组该位置的头结点的监视器锁
           synchronized (f) {
               if (tabAt(tab, i) == f) {
                   if (fh >= 0) { // 头结点的 hash 值大于 0，说明是链表
                       // 用于累加，记录链表的长度
                       binCount = 1;
                       // 遍历链表
                       for (Node<K,V> e = f;; ++binCount) {
                           K ek;
                           // 如果发现了"相等"的 key，判断是否要进行值覆盖，然后也就可以 break 了
                           if (e.hash == hash &&
                               ((ek = e.key) == key ||
                                (ek != null && key.equals(ek)))) {
                               oldVal = e.val;
                               if (!onlyIfAbsent)
                                   e.val = value;
                               break;
                           }
                           // 到了链表的最末端，将这个新值放到链表的最后面
                           Node<K,V> pred = e;
                           if ((e = e.next) == null) {
                               pred.next = new Node<K,V>(hash, key, value, null);
                               break;
                           }
                       }
                   }
                   else if (f instanceof TreeBin) { // 红黑树
                       Node<K,V> p;
                       binCount = 2;
                       // 调用红黑树的插值方法插入新节点
                       if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                      value)) != null) {
                           oldVal = p.val;
                           if (!onlyIfAbsent)
                               p.val = value;
                       }
                   }
               }
           }
           // binCount != 0 说明上面在做链表操作
           if (binCount != 0) {
               // 判断是否要将链表转换为红黑树，临界值和 HashMap 一样，也是 8
               if (binCount >= TREEIFY_THRESHOLD)
                   // 这个方法和 HashMap 中稍微有一点点不同，那就是它不是一定会进行红黑树转换，
                   // 如果当前数组的长度小于 64，那么会选择进行数组扩容，而不是转换为红黑树
                   //    具体源码我们就不看了，扩容部分后面说
                   treeifyBin(tab, i);
               if (oldVal != null)
                   return oldVal;
               break;
           }
       }
   }
   // 
   addCount(1L, binCount);
   return null;
  }
```
  *详细步骤：*

  - 获取table数组

  - 若为null,则初始化initTable

  - 通过hash值找到对应的hash桶，若该桶中没有元素，则先采用CAS机制put新节点，成功则跳出循环

  - 如果当前节点hash值为-1，则说明正在扩容，去帮助扩容helpTransfer(tab, f)

  - 否则对该头节点进行加锁。

  - 如果当前结点hash值>=0，则说明是链表形式，遍历链表，若找到hash值相同且key.equals(ek)，则更新该节点中的value，若未找到则在链表尾添加新节点。

  - 如果当前结点hash值小于0，判断如果 f instanceof TreeBin ，那么就是红黑树的结构。

  - 如果再链表中添加节点，判断链表长度是否达到了8，是否需要转换为红黑树。

- 检索操作get不阻塞，很可能和更新操作重叠，总能得到最近更新完成的值。

  ```java
  static class Node<K,V> implements Map.Entry<K,V> {
          final int hash;
          final K key;
          volatile V val;
          volatile Node<K,V> next;    // next 也具有 volatile 的特性，可以保证总能得到最近更新完成的值。
          // ....
  }
  ```

  

  size() 方法可以直接获取 ConcurrentHashMap 中元素的个数。CounterCell 静态内部类包含一个 volatile long value，此 value 表示每一个 hash 桶中元素的数量。然后使用一个 CounterCell 的数组保存每个 hash 中的元素个数。求 size 时只需要统计对数组累加即可。当添加或者删除节点时，采用 CAS 机制修改 CounterCell 数组中的元素的值。

  ```java
  @sun.misc.Contended static final class CounterCell {
      volatile long value;
      CounterCell(long x) { value = x; }
  }
  ```

  

  扩容：tryPresize
  sizeCtl 字段用于初始化和扩容时，为负数表示正在初始化或者扩容，-1表示初始化，该数+1表示正在扩容的线程数。

  

  数据迁移：transfer
  transferIndex 字段表示扩容时，下一个任务的位置。



#### Collections.synchronizedMap

类似与 HashTable 的实现，为每一个方法都加上 synchronized 关键字。

SynchronizedMap 为 Collections 类中的一个内部类，保存一个map对象和一个Object对象的互斥量。

```java
    private final Map<K,V> m;     // Backing Map
    final Object      mutex;            // Object on which to synchronize
    SynchronizedMap(Map<K,V> m) {
        this.m = Objects.requireNonNull(m);
        mutex = this;        // mutex默认为当前对象，也可以传递一个对象
    }
```



然后调用 map 对象时，先对 mutex 加锁，然后再调用原来 map 的自身的方法。

```java
    public int size() {
            synchronized (mutex) {return m.size();}
    }
```



#### LinkedHashMap

为 HashMap 的每个元素添加一个前驱和后继指针形成双链表结构，通过 accessOrder 字段设置链表顺序，为 true ,则按 LRU 顺序（每次访问将元素移到链尾）；为 false 则按插入顺序。