---
title: Java 异常与反射
date: 2018-11-30 10:25:41
tags: [Java异常,Java反射,笔记整理,面经]
categories: [Java 学习]
---



##### Throwable Java异常的基类

![img](https://mubu.com/document_image/34564eaa-c11d-4197-baba-e099c5c74909-983181.jpg)

- **Error**: 用于标记严重错误。合理的应用程序不应该去 try/catch 这种错误。绝大多数的错误都是非正常的，就根本不该出现的。常见的有OOM、StackOverflowError。
- **Exception**: 用于指示一种合理的程序想去 catch 的条件。即它仅仅是一种程序运行条件，而非严重错误，并且鼓励用户程序去 catch 它。

<!-- more -->

Error和RuntimeException 及其子类都是未检查的异常（unchecked exceptions），而所有其他的Exception类都是检查异常（checked exceptions）



checked exceptions

​	通常是从一个可以恢复的程序中抛出来的，并且最好能够从这种异常中使程序恢复。比如 **ParseException(DateFormat日期转换时)、IOException、SQLException、InterruptedException(Thread.sleep方法)、ClassNotFoundException、FileNotFoundException**等。检查异常发生在编译阶段，必须要使用 try…catch（或者throws）否则编译不通过。

unchecked exceptions

​	通常是如果一切正常的话本不该发生的异常，但是的确发生了。发生在运行期，具有不确定性，主要是由于程序的逻辑问题所引起的。比如**IndexOutOfBoundException、ClassCastException(强制类型转换)、NullPointerException、NumberFormatException(Integer.valueOf("123a");)、ArithmeticException(15/0)、ConcurrentModificationException(迭代时快速失败)、NoSuchMethodException**等。这类错误本身就是bug，应该被修复，出现此类错误时程序就应该立即停止执行。因此，面对 Errors 和 unchecked exceptions 应该让程序自动终止执行，程序员不该做诸如 try/catch 这样的事情，而是应该查明原因，修改代码逻辑。



##### 一个线程运行时发生异常会怎样？

存在两种情形(属于运行时异常)：

​	① 如果该异常被捕获或抛出，则程序继续运行。

​	② 如果异常没有被捕获该线程将会停止执行。

Thread.UncaughtExceptionHandler 是用于处理未捕获异常造成线程突然中断情况的一个内嵌接口。当一个未捕获异常将造成线程中断的时候JVM会使用Thread.getUncaughtExceptionHandler() 来查询线程的 UncaughtExceptionHandler ，并将线程和异常作为参数传递给 handler 的 uncaughtException() 方法进行处理。



##### Java 反射

###### 概述

在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取信息以及动态调用对象的方法的功能就是反射。

###### 使用的前提条件

- 必须先得到代表字节码的Class对象，Class类用于表示.class文件（字节码）。
- 在运行期间，一个类，只有一个Class对象产生。

###### 获取Class对象的三种方式

- Object ——> getClass();
- 任何数据类型（包括基本数据类型）都有一个“静态”的class属性 Student.class
- 通过Class类的静态方法：Class.forName(String  className) (常用) JDBC加载驱动

比较： 源自Java 编程思想

- Student.class 只是加载类，不会进行初始化操作。

- Class.forName(String  className) 会加载类，同时链接、初始化（类加载流程）

  ![img](https://mubu.com/document_image/fa73d98b-d2a1-4a5d-9e48-2cd1e4b2475d-983181.jpg)

###### 使用规范

**获取构造方法**

- 批量的方法：

  - public Constructor[] getConstructors()：所有"公有的"构造方法
  - public Constructor[] getDeclaredConstructors()：获取所有的构造方法(包括私有、受保护、默认、公有)

- 获取单个的方法，并调用：

  - public Constructor getConstructor(Class... parameterTypes): 获取单个的"公有的"构造方法
  - public Constructor getDeclaredConstructor(Class... parameterTypes):获取"某个构造方法"可以是私有的，或受保护、默认、公有

- 调用构造方法：

  - Constructor-->newInstance(Object... initargs)

  

**获取成员变量并调用**

- 批量的

  - Field[] getFields():获取所有的"公有字段"
  - Field[] getDeclaredFields():获取所有字段，包括：私有、受保护、默认、公有

- 获取单个的

  - public Field getField(String fieldName):获取某个"公有的"字段
  - public Field getDeclaredField(String fieldName):获取某个字段(可以是私有的)

- 设置字段的值：

  - Field --> public void set(Object obj,Object value):
  - 参数说明：obj:要设置的字段所在的对象 value:要为字段设置的值；

  

**获取成员方法并调用**

- 批量的：

  - public Method[] getMethods():获取所有"公有方法"；（包含了父类的方法也包含Object类）
  - public Method[] getDeclaredMethods():获取所有的成员方法，包括私有的(不包括继承的)

- 获取单个的：

  - public Method getMethod(String name,Class<?>... parameterTypes): 参数： name : 方法名；Class ... : 形参的Class类型对象
  - public Method getDeclaredMethod(String name,Class<?>... parameterTypes)

- 调用方法：

  - Method --> public Object invoke(Object obj,Object... args)
  - 参数说明：obj : 要调用方法的对象； args:调用方式时所传递的实参；

  

**反射 main 方法**，对于静态方法 getMethod 方法的第一个参数为null

**通过反射运行配置文件内容**，通过修改配置文件就可以实现对不同的类进行反射

**通过反射越过泛型检查**

**通过反射获得方法的参数名称**

​	JDK 1.8 *通过设置编译时是否保留参数名称*，否则参数名称将会变成 arg1、arg2等。勾选最下面的一个选项即可。

![img](https://mubu.com/document_image/0cc9ab96-dacf-411c-8a19-8351c2203797-983181.jpg)

​	案例：

```java
    // 获取某方法的参数名称
    public static List<String> getParameterNameJava8(Class clazz, String methodName) {
        List<String> lists = new ArrayList<>();
        Method[] methods = clazz.getDeclaredMethods();
        for (int i = 0; i < methods.length; i++) {
            if (methods[i].getName().equals(methodName)) {
                Parameter[] p = methods[i].getParameters();
                for (Parameter parameter : p) {
                    lists.add(parameter.getName());
                }
            }
        }
        return lists;
    }
```



##### Java 新特性

https://www.cnblogs.com/tony-yang-flutter/p/3503935.html

- jdk 1.5

  - 枚举
  - for-each
  - 泛型
  - 自动拆箱和装箱功能
  - 可变参数
  - 静态导入
  - Lock 并发包

  

- jdk 1.6

  - synchronized 的升级 (自旋、锁消除、锁粗化)

  

- jdk 1.7

  - 泛型推断
    Map<String, List<String>> map = new HashMap<>();

  - 二进制面值
    int i = 0b1010; // 10

  - 数字变量对下滑线的支持
    int i = 12_3;

  - switch对String的支持

  - Try-with-resources

    > **利用Try-Catch-Finally管理资源（旧的代码风格）**
    >
    > - 即使try语句块中抛出的异常与异常传播更相关，最终还是finally语句块中抛出的异常会根据调用栈向外传播。会抛出finally语句块中资源关闭的异常。
    >
    > **try-with-resources管理资源**
    >
    > - 当try-with-resources结构中抛出一个异常，同时FileInputStream被关闭时（调用了其close方法）也抛出一个异常，try-with-resources结构中抛出的异常会向外传播，而FileInputStream关闭时抛出的异常被抑制了。
    >
    > **AutoCloseable和Closeable的关系**
    >
    > - java.lang.AutoCloseable
    >
    >   - Java 7引入，为所有可以关闭的对象提供资源释放实现，成为java.io.Closeable的父接口
    >   - 使用Java 7以及更高版本实现接口后可以使用try-with-resource语法实现自动释放资源
    >   - close()方法声明抛出java.lang.Exception，意味着接口实现类的close()方法可以声明抛出Exception或者Exception的子类
    >   - close()方法不需要保证多次调用不产生副作用
    >
    > - java.io.Closeable
    >
    >   - Java 5引入，为流对象提供资源释放实现
    >   - 使用Java 7及更高版本实现接口后可以使用try-with-resource语法实现自动释放资源
    >   - close()方法声明抛出java.io.IOException，意味着接口实现类的close()方法只能声明抛出IOException或者IOException的子类
    >   - close()方法需要保证多次调用不产生副作用
    >
    >   

- jdk 1.8

  - Lambda 表达式 <https://www.cnblogs.com/franson-2016/p/5593080.html>

    - 编译器推断并帮你转换包装为常规的代码,因此你可以使用更少的代码来实现同样的功能。
    - 基本语法:
      - (parameters) -> expression
      - (parameters) ->{ statements; }

  - 接口的默认方法 <https://blog.csdn.net/qq_40369829/article/details/79356133>

    *默认方法 default :*

    - 可以通过实现接口的类实例化的对象来调用，也可以被重写。
    - 是 public 的。

    *静态方法 static：*

    只能在本接口中调用，实现类中也不能调用。职责上是工具方法

    ```java
    public interface A {
        int get();
        static int getStatic() {
            return 1;
        }
        default int getDefault() {
            getStatic();
            return 1;
        }
    }
    ```

  - 注解(Annotation)

     <https://www.cnblogs.com/xdp-gacl/p/3622275.html>

  - Fork/Join 框架

    - 在必要的情况下，将一个大任务，进行拆分（fork）成若干个子任务（拆到不能再拆，这里就是指我们制定的拆分的临界值），再将一个个小任务的结果进行join汇总。
    - Fork/Join与传统线程池的区别
      - Fork/Join采用“工作窃取模式”，当执行新的任务时他可以将其拆分成更小的任务执行，并将小任务加到线程队列中，然后再从一个随即线程中偷一个并把它加入自己的队列中。
      - 就比如两个CPU上有不同的任务，这时候A已经执行完，B还有任务等待执行，这时候A就会将B队尾的任务偷过来，加入自己的队列中，对于传统的线程，ForkJoin更有效的利用的CPU资源！

  - optional <https://blog.csdn.net/aitangyong/article/details/54564100>

    - 使用 Optional 避免 null 导致的 NullPointerException
    - Optional.of() 或者 Optional.ofNullable()：创建 Optional 对象，差别在于 of 不允许参数是null，而 ofNullable 则无限制。
    - Optional.empty()：所有null包装成的Optional对象
    - isPresent()：判断值是否存在
    - ifPresent(Consumer consumer)：如果option对象保存的值不是null，则调用consumer对象，否则不调用
    - orElse(value)：如果optional对象保存的值不是null，则返回原来的值，否则返回value参数

  - jvm 元空间代替永久代

  - HashMap 链表长度>=8时，转换为红黑树；取消 Segment 使用 CAS 和 Synchronized 进行锁操作。