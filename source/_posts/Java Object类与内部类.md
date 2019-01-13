---
title: Java Object类与内部类
date: 2018-11-24 10:25:41
tags: [Java内部类,笔记整理,面经]
categories: [Java 学习]
---



##### Object 类

Java Object类为所有类的基类，所有类都是该类的子类。有时候面试官也会问到Object类中存在哪些方法等问题。

**包含的所有方法**

9个 public 方法（4+5）

- getClass()、hashCode()、equals(Object obj)、toString()
- notify()、notifyAll()、wait()、wait(long timeout)、wait(long timeout, int nanos)

2个 protect 方法

- clone()、finalize()

<!-- more -->

JDK1.8 Object类源码

```java
public class Object {

    private static native void registerNatives();
    static {
        registerNatives();
    }
    
    public final native Class<?> getClass();
    
    public native int hashCode();
    
    public boolean equals(Object obj) {
        return (this == obj);
        
    }
    
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
    
    public final native void notify();
    
    public final native void notifyAll();
    
    public final native void wait(long timeout) throws InterruptedException;
    
    public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }

        if (nanos > 0) {
            timeout++;
        }

        wait(timeout);
    }
    
    public final void wait() throws InterruptedException {
        wait(0);
    }
    
    protected void finalize() throws Throwable { }
    
    protected native Object clone() throws CloneNotSupportedException;
}
```



**wait() 与 notify()/notifyAll()**

wait(): 释放占有的对象锁，线程进入等待池，释放cpu，而其他正在等待的线程即可抢占此锁，获得锁的线程即可运行程序。

Thread.sleep()：此方法为**线程方法**，线程调用此方法后，会休眠一段时间，休眠期间，会暂时释放cpu，但并不释放对象锁。

notify(): 该方法会唤醒因为调用对象的wait()而等待的线程，其实就是对对象锁的唤醒，从而使得wait()的线程可以有机会获取对象锁。调用notify()后，并不会立即释放锁，而是继续执行当前代码，直到 synchronized 中的代码全部执行完毕，才会释放对象锁。JVM则会在等待的线程中调度一个线程去获得对象锁，执行代码。*注意：wait() 方法需要 try/catch 包裹（会出现 InterruptedException 检查异常），另外wait()和notify()必须在 synchronized 代码块中（否则出现 IllegalMonitorStateException 运行时异常）。*

notifyAll()则是唤醒所有等待的线程。



##### 内部类 <https://www.cnblogs.com/chenssy/p/3388487.html>

###### 为什么要使用内部类

​	每个内部类都能独立地继承一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响。内部类使得多重继承的解决方案变得更加完整。

​	特性

1、内部类可以用多个实例，每个实例都有自己的状态信息，并且与其他外围对象的信息相互独立。

2、在单个外围类中，可以让多个内部类以不同的方式实现同一个接口，或者继承同一个类。

3、创建内部类对象的时刻并不依赖于外围类对象的创建。

4、内部类并没有令人迷惑的“is-a”关系，他就是一个独立的实体。

5、内部类提供了更好的封装，除了该外围类，其他类都不能访问。



###### 成员内部类

- 成员内部类中不能存在任何 static 的变量和方法

- 成员内部类是依附于外围类的，所以只有先创建了外围类才能够创建内部类。

- 推荐使用 getxxx() 来获取成员内部类，尤其是该内部类的构造函数无参数时 。

  ```java
  public class OuterClass {
      private String str;
      
      public void outerDisplay(){
          System.out.println("outerClass...");
      }
      
      public class InnerClass{
          public void innerDisplay(){
              //使用外围内的属性
              str = "chenssy...";
              System.out.println(str);
              //使用外围内的方法
              outerDisplay();
          }
      }
      
      /*推荐使用getxxx()来获取成员内部类，尤其是该内部类的构造函数无参数时 */
      public InnerClass getInnerClass(){
          return new InnerClass();
      }
      
      public static void main(String[] args) {
          OuterClass outer = new OuterClass();
          OuterClass.InnerClass inner = outer.getInnerClass();
          inner.innerDisplay();
      }
  }
  ```

  

###### 局部内部类

- 嵌套在方法和作用于内
- 对于这个类的使用主要是应用与解决比较复杂的问题，想创建一个类来辅助我们的解决方案，到那时又不希望这个类是公共可用的，所以就产生了局部内部类，局部内部类和成员内部类一样被编译，只是它的作用域发生了改变，它只能在该方法和属性中被使用，出了该方法和属性就会失效。



###### 匿名内部类

- 匿名内部类是没有访问修饰符的

- new 匿名内部类，这个类首先是要存在的。如果我们将那个InnerClass接口注释掉，就会出现编译出错。

- 注意getInnerClass()方法的形参，第一个形参是用final修饰的，而第二个却没有。同时我们也发现第二个形参在匿名内部类中没有使用过，所以**当所在方法的形参需要被匿名内部类使用，那么这个形参就必须为final。**

- 匿名内部类是没有构造方法的。

  ```java
  public class OuterClass {
      public InnerClass getInnerClass(final int num,String str2){
          return new InnerClass(){
              int number = num + 3;
              public int getNumber(){
                  return number;
              }
          };        /* 注意：分号不能省 */
      }
      
      public static void main(String[] args) {
          OuterClass out = new OuterClass();
          InnerClass inner = out.getInnerClass(2, "chenssy");
          System.out.println(inner.getNumber());
      }
  }
  interface InnerClass {
      int getNumber();
  }
  ```

  

###### 静态内部类

- 它的**创建是不需要依赖于外围类**的
- 它**不能使用任何外围类的非static成员变量和方法**