---
title: Java 类加载和双亲委派模型
date: 2018-11-28 20:48:41
tags: [Java类加载机制,笔记整理,面经]
categories: [Java 学习]
---



#### 类加载过程

加载，连接(验证，准备，解析)，初始化

![img](https://mubu.com/document_image/1274e1ce-70d8-467b-b8ba-de9c51d3fb9a-983181.jpg)

<!-- more -->

##### 类加载阶段总共分为五个阶段：

**加载**

在内存中生成一个代表这个类的 java.lang.Class 对象，作为方法区这个类的各种数据的入口。注意这里不一定非得要从一个 class 文件获取，这里既可以从ZIP包中读取（比如从jar包和war包中读取），也可以在运行时计算生成（动态代理），也可以由其它文件生成（比如将JSP文件转换成对应的Class类）



**验证**

为了确保 Class 文件的字节流中包含的信息是否符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

包含四种验证：

1. 文件格式验证

验证字节流是否符合 class 文件格式的规范，并且能被当前版本的虚拟机处理

2. 元数据验证

对字节码描述的信息进行语义分析，以保证其描述的信息符合Java语言规范的要求

3. 字节码验证

进行数据流和控制流分析

4. 符号引用验证



**准备**

为类的静态变量分配内存并将其初始化为默认值,这些内存都将在方法区中进行分配。准备阶段不分配类中的实例变量的内存,实例变量将会在对象实例化时随着对象一起分配在Java 堆中。静态常量会直接赋值。

默认值

![img](https://mubu.com/document_image/610c4fad-fb70-456d-96c3-7bb615f0edc0-983181.jpg)



**解析**

虚拟机将常量池中的符号引用替换为直接引用的过程。

> 符号引用：以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义地定位到目标即可。符号引用与虚拟机实现的内存布局无关，引用的目标并不一定已经加载到内存中。
>
> 直接引用：指向目标的指针，相对偏移量或是一个能间接定位到目标的句柄。如果有了直接引用，那引用的目标必定已经在内存中存在。



**初始化**

类加载最后一个阶段，前面的类加载阶段之后，除了在加载阶段可以自定义类加载器以外，其它操作都由JVM主导。到了初始化阶段，才开始真正执行类中定义的Java程序代码。

初始化阶段是**执行类构造器 clinit() 方法**的过程。clinit() 方法是由编译器自动收集类中的所有**类变量**的赋值动作和**静态语句块(static{}块)**中的语句合并产生的。





#### 类加载器

##### 分类

###### 启动类加载器(Bootstrap ClassLoader)

负责加载 JAVA_HOME\lib 目录中的，或通过-Xbootclasspath参数指定路径中的，且被虚拟机认可（按文件名识别，如rt.jar）的类。<u>使用C++实现，属于虚拟机的自身的一部分。而其他的类加载器使用Java语言实现，独立于虚拟机外部，并且全部继承自java.lang.ClassLoader。</u>



###### 扩展类加载器(Extension ClassLoader)

负责加载 JAVA_HOME\lib\ext 目录中的，或通过java.ext.dirs系统变量指定路径中的类库。



###### 应用程序类加载器(Application ClassLoader)

负责加载用户路径（classpath）上的类库。可以通过ClassLoader.getSystemClassLoader()来获取它。



###### 用户自定义类加载器

通过继承 java.lang.ClassLoader 类的方式实现。



##### 自定义加载器

1. 继承 ClassLoader 类并重写 findClass 方法。否则会报运行时异常。

```java
protected Class<?> findClass(String name) throws ClassNotFoundException {
     throw new ClassNotFoundException(name);
 }
```

1. 如果想打破双亲委派模型则需要重写 loadClass 方法。默认的 loadClass 方法是实现了双亲委派机制的逻辑，即会先让父类加载器加载，当无法加载时才由自己加载。

```java
protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // 首先检查请求的类是否已经被加载过
            Class<?> c = findLoadedClass(name);
            if (c == null) {	// 未被加载
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);	// 调用父加载器的 loadclass
                    } else {
                        c = findBootstrapClassOrNull(name); // 父加载器为空时，使用启动类加载器
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
```





#### 双亲委派模型

##### 定义

当一个类加载器收到类加载任务，会先交给其父类加载器去完成，因此最终加载任务都会传递到顶层的启动类加载器，只有当父类加载器无法完成加载任务时，才会尝试执行加载任务。

![img](https://mubu.com/document_image/136b94af-57ea-4e10-b05f-41996811545d-983181.jpg)

<u>注：启动类加载器无法被 java 程序直接引用，用户在编写自定义类加载器时，如果需要把请求加载委派给引导类加载器，那么字节使用 null 即可。</u>

好处：比如加载位于rt.jar包中的类 java.lang.Object，不管是哪个加载器加载这个类，最终都是委托给顶层的启动类加载器进行加载，这样就保证了使用不同的类加载器最终得到的都是同样一个Object对象。**保证类的唯一性**、 **因类加载器的分层使得类也分层次加载(带有优先级)**。



##### 打破双亲委派模型的三种情况

1.  双亲委派模型与JDK1.2时引入的，为了向前兼容，新增一个 protected 的 findClass() 方法。在此之前用户继承 ClassLoader 类就是为了重写 loadClass() 方法。而之后则不推荐覆盖 loadClass() 方法，而是**把类加载逻辑写到 findClass() 方法中**。

2. 双亲委派很好的解决了各个类加载器的基础类的统一问题。但是如果基础类需要回调用户代码则无法实现。*比如 JNDI（Java 命名和目录接口），由启动类加载器加载，实现对资源进行集中管理和查找，它需要调用由独立厂商实现并部署在应用程序的 ClassPath 下的 JNDI 接口提供者（SPI），但是启动类加载器不可能“认识”这些代码。JDBC、JCE、JAXB、JBI也是如此。*

   解决方案为：**线程上下文类加载器**。该类加载器通过 Thread 类的 setContextClassLoader() 方法进行设置。 如果创建线程时没有还未设置，则从父线程中继承一个，如果应用程序的全局范围内都没有设置过的话，那么该类加载器就是应用程序类启动器。

   然后上下文类加载器去加载所需要的SPI代码，也就是父类加载器请求子类加载器去完成类加载的动作。

3. 由于用户对程序动态性的追求而导致。比如**代码热替换、模块热部署**等。

   OSGI（Open Service Gateway Initiative）开放服务网关协议为 Java 动态化模块化系统的一系列规范。而 **OSGI 实现模块化热部署的关键则是它自定义的类加载器机制的实现**。每一个程序模块（OSGI中称为 Bundle）都有一个自己的类加载器，当需要更换一个 Bundle 时，就把 Bundle 连同类加载器一起换掉以实现代码的热替换。

   在OSGI环境下，类加载器不再是双亲委派模型的树状结构，而是进一步发展为更加复杂的网状结构。加载流程为：

   > 1. 将以 java.* 开头的类委派给父类加载器加载
   > 2. 否则，将委派列表名单内的类委派给父类加载器加载
   > 3. 否则，将 Import 列表中的类委派给 Export 这个类的 Bundle 的类加载器加载
   > 4. 否则，查找当前 Bundle 的 ClassPath ，使用自己的类加载器加载
   > 5. 否则，查找类是否在自己的 Fragment Bundle 中，如果在，则委派给 Fragment Bundle 的类加载器加载
   > 6. 否则，查找 Dynamic Import 列表的 Bundle，委派给对应的 Bundle 的类加载器加载
   > 7. 否则，类查找失败。



#### 常见问题

##### 何时开始类的初始化（主动引用）

1. 创建类的实例
2. 访问类的静态变量(除常量【被final修辞的静态变量】)
3. 访问类的静态方法
4. 反射如(Class.forName("my.xyz.Test"))
5. 当初始化一个类时，发现其父类还未初始化，则先出发父类的初始化（对于接口，使用时才会去初始化父接口）
6. 虚拟机启动时，定义了Main()方法的那个类先初始化



##### 不会初始化类的情况 （被动引用）

1. **子类调用父类的静态变量，子类不会被初始化**。只有父类被初始化。对于静态字段，只有直接定义这个字段的类才会被初始化
2. 通过数组定义来引用类，不会触发类的初始化  SubClass[] arr= new SubClass[10];
3. 访问类的常量，不会初始化类
4. 反射通过 .class 获取 class 对象时
5. 反射通过 Class.forName("my.xyz.Test",false,ClassLoader.getSystemClassLoader()) 第二个参数代表是否初始化类



##### 测试

```java
package com.java;
public class Main {
	public static void main(String[] args) {
		System.out.println(Father.FAN); 			 // 访问常量
		Class clazz = Father.class;					// .class 获取 class 对象
		System.out.println(clazz.getName());		 // 输出全限定名
		System.out.println(clazz.getSimpleName());    // 输出类名(不包含包名)
		Father[] f = new Father[10];				// 数组定义来引用类
		try {
			Class.forName("Father",false,ClassLoader.getSystemClassLoader());  // 也不会初始化
			System.out.println("----------");
			Class.forName("Father");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
		
	}
	
}
class Father{
	public static final int FAN = 1;
	static {
		System.out.println("Father类初始化");
	}
}
```

运行结果图

![img](https://mubu.com/document_image/6c5df023-bda6-4b36-ad9c-bd1f7b5e652c-983181.jpg)