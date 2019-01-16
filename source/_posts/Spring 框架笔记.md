---
title: Spring 框架笔记
date: 2019-1-5 22:01:41
tags: [Spring,Spring MVC,笔记整理,面经]
categories: [Spring 学习]
---



### 七大模块

1. Spring Core：Core 封装包是框架的最基础部分，提供 IOC 和依赖注入特性。这里的基础概念是BeanFactory，它提供对 Factory 模式的经典实现来消除对程序性单例模式的需要，并真正地允许你从程序逻辑中分离出依赖关系和配置。
2. Spring Context： 构建于Core封装包基础上的 Context 封装包，提供了一种框架式的对象访问方法，有些像 JNDI 注册器。Context 封装包的特性得自于Beans封装包，并添加了对国际化（I18N）的支持（例如资源绑定），事件传播，资源装载的方式和 Context 的透明创建，比如说通过Servlet容器。
3. Spring DAO:  DAO (Data Access Object)提供了 JDBC 的抽象层，它可消除冗长的 JDBC 编码和解析数据库厂商特有的错误代码。 并且，JDBC封装包还提供了一种比编程性更好的声明性事务管理方法，不仅仅是实现了特定接口，而且对所有的 POJOs（plain old Java objects）都适用。<!--more-->
4. Spring ORM：ORM 封装包提供了常用的“**对象/关系**”映射APIs的集成层。 其中包括JPA、JDO、Hibernate 和 iBatis 。利用ORM封装包，可以混合使用所有 Spring 提供的特性进行“对象/关系”映射，如前边提到的简单声明性事务管理。
5. Spring AOP：Spring的 AOP 封装包提供了符合AOP Alliance规范的面向方面的编程实现，让你可以定义，例如方法拦截器（method-interceptors）和切点（pointcuts），从逻辑上讲，从而减弱代码的功能耦合，清晰的被分离开。而且，利用source-level的元数据功能，还可以将各种行为信息合并到你的代码中。
6. Spring Web：Spring 中的 Web 包提供了基础的针对Web开发的集成特性，例如多方文件上传，利用Servlet listeners进行IOC容器初始化和针对Web的 ApplicationContext。当与WebWork或Struts一起使用Spring时，这个包使Spring可与其他框架结合。
7. Spring Web MVC：Spring中的MVC封装包提供了Web应用的Model-View-Controller（MVC）实现。Spring的MVC框架并不是仅仅提供一种传统的实现，它提供了一种清晰的**分离模型**，在领域模型代码和Web Form之间。并且，还可以借助Spring框架的其他特性。



### Ioc (Inverse of Control)

**控制反转**将依赖对象的创建和管理交由Spring容器，而**依赖注入**(Dependency Injection)则是在控制反转的基础上将 Spring 容器管理的依赖对象注入到应用之中。

将对象的创建从**编译时延期到运行时**，即通过配置进行加载，是纯粹的针对接口编程。解除了程序对具体实现的依赖,可以灵活的组装各种实现，因此可以轻松的将各种成熟的工具继承到Spring中来。

Spring IoC 容器主要是基于 BeanFactory 和 ApplicationContext 两个接口,其中 ApplicationContext 又是 BeanFactory 的子接口。所以,换句话说,BeanFactory 是 Spring IoC 容器的顶层接口,而 ApplicationContext 是最常用接口。

 Spring IoC 容器利用 Java 的 POJO 类和配置元数据（XML，Java 注释或 Java 代码）来生成完全配置和可执行的系统或应用程序。

![img](https://mubu.com/document_image/e55ed554-01db-4e37-a5c4-cc383f7fd329-983181.jpg)



### AOP

面向方面（切面）编程，作为面向对象的一种补充，用于**处理系统中分布于各个模块的横切关注点**，比如事务管理、日志、缓存、权限认证、性能分析等等。将辅助性逻辑与主逻辑进行分离。

AOP实现的关键在于AOP框架自动创建的AOP代理，AOP代理主要分为**静态代理**和**动态代理**，静态代理的代表为AspectJ；而动态代理则以Spring AOP为代表。

- AspectJ 是静态代理的增强，所谓的静态代理就是AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强。在编译阶段将 Aspect 织入 Java 字节码中， 运行的时候就是经过增强之后的AOP对象。
- Spring AOP 使用的动态代理，所谓的动态代理就是说 AOP 框架不会去修改字节码，而是在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。包括JDK动态代理和CGLIB动态代理。
  - **JDK动态代理**通过<u>反射</u>来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是 InvocationHandler 接口和 Proxy 类。
  - **CGLIB（Code Generation Library）**，是一个代码生成的类库，可以在运行时动态的生成某个类的子类。注意：CGLIB 是通过<u>继承</u>的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。



### Spring Bean 的生命周期



![img](https://mubu.com/document_image/de557ef2-483e-4393-9c91-90ee1ac5b1b6-983181.jpg)

1. 实例化 Bean

   - 对于 BeanFactory 容器，当客户向容器请求一个尚未初始化的 bean 时，或初始化 bean 的时候需要注入另一个尚未初始化的依赖时，容器就会调用 createBean 进行实例化。
   - 对于 ApplicationContext 容器，当容器启动结束后，便实例化所有的 bean。

2. 设置对象属性（依赖注入）

3. 注入Aware接口

   Spring会检测该对象是否实现了xxxAware接口，并将相关的xxxAware实例注入给bean。BeanNameAware、BeanFactoryAware、ApplicationContextAware。

4. BeanPostProcessor 接口方法（前置处理）

   执行 postProcessBeforeInitialzation( Object bean, String beanName ) 

5. 若 Bean 实现了 InitializingBean 接口，并重写了 afterPropertiesSet 方法，可在该方法中添加我们自定义的逻辑，则 Spring 会在前置处理完成后执行 afterPropertiesSet 函数

6. Spring为了降低对客户代码的侵入性，给 bean 的配置提供了 init-method 属性，该属性指定了在这一阶段需要执行的函数名。Spring便会在初始化阶段执行我们设置的函数

7. BeanPostProcessor 接口方法（后置处理）

   执行 postProcessAfterInitialzation( Object bean, String beanName ) 

8. DisposableBean和destroy-method

   通过给 destroy-method 指定函数，就可以在 bean 销毁前执行指定的逻辑。



### BeanFactory 和 ApplicationContext 有什么区别

#### BeanFactory

Spring IoC 容器的**顶层接口**。BeanFactory 接口定义了 Spring IoC 中最重要的方法(簇): getBean() 方法。这个方法用于从 Spring IoC 容器中获得 Bean 。

#### ApplicationContext

**最常用接口**，由 BeanFactory 接口派生而来。拥有 BeanFactory 接口的所有功能同时还具有以下功能：

提供国际化的消息访问 （扩展了MessageSource接口）

资源访问，如 URL 和文件（扩展了ResourceLoader(资源加载器)接口）

事件传播

载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层



### Spring 框架中用到了哪些设计模式

#### 单例模式

Spring下默认的 bean 均为 singleton，可以通过 singleton=“true|false” 或者 scope=“？” 来指定

Spring 的单例模式为可继承的单例类，使用的是**单例注册表**的方式

- 使用 map 实现注册表
- 使用 protect 修饰构造方法（其它单例方法构造方法都是私有的，就无法实现继承）

#### 工厂模式

将对象的创建和使用相分离，将对象的创建及初始化职责交给工厂对象。 BeanFactory

#### 代理模式

（1、JDK动态代理。2、CGLib字节码生成技术代理。）对类进行方法级别的切面增强，即生成被代理类的代理类， 并在代理类的方法前，设置拦截器，通过执行拦截器中的内容增强了代理方法的功能，实现的面向切面编程。

#### 观察者模式

定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。 ApplicationListener

#### 策略模式

定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换。可使得算法可独立于使用它的客户而变化

#### 模板方法

定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤



### Spring 事务实现方式

#### 编程式事务管理

将事务管理代码嵌入到业务方法中来控制事务的提交和回滚。在编程式管理事务时, 必须在每个事务操作中包含额外的事务管理代码。

#### 声明式事务管理

将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理。事务管理作为一种横切关注点，可以通过 AOP 方法模块化。Spring 通过 Spring AOP 框架支持声明式事务管理。

Spring 从不同的事务管理 API 中抽象了一整套的事务机制。开发人员不必了解底层的事务 API，就可以利用这些事务机制。有了这些事务机制。事务管理代码就能独立于特定的事务技术了。

1. 用**事务通知**声明式地管理事务

![img](https://mubu.com/document_image/ad108838-efaf-4f8d-ac57-61de4c927f66-983181.jpg)

2. 用 **@Transactional 注解**声明式地管理事务

![img](https://mubu.com/document_image/e36a9323-a91f-4314-b6fd-b52411fc6b07-983181.jpg)

为了将方法定义为支持事务处理的，可以为方法添加 @Transactional 注解。根据 Spring AOP 基于代理机制，只能标注公有方法。

可以在方法或者类级别上添加 @Transactional 注解。当把这个注解应用到类上时，这个类中的所有公共方法都会被定义成支持事务处理的。

在 Bean 配置文件中只需要启用`<tx:annotation-driven>` 元素，并为之指定事务管理器就可以了。

如果事务处理器的名称是 transactionManager，就可以在`<tx:annotation-driven>` 元素中省略 transaction-manager 属性。这个元素会自动检测该名称的事务处理器。



### Spring 事务的传播机制 

> https://www.cnblogs.com/softidea/p/5962612.html

事务传播行为规定了事务方法和事务方法发生嵌套调用时事务是如何传播的。

#### 事务传播类型（七种）

![img](https://mubu.com/document_image/dfaffca5-af2d-4f7e-868c-2331f47647d8-983181.jpg)



### Spring 自定义注解

> <https://www.jianshu.com/p/7c2948f64b1c> 



### Spring MVC 启动流程

#### web.xml 配置

```xml
<web-app>
  <display-name>Web Application</display-name>
  <!--全局变量配置-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext-*.xml</param-value>
  </context-param>

  <!--监听器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  
  <!--解决乱码问题的filter-->
  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>utf-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  <!--Restful前端控制器-->
  <servlet>
    <servlet-name>springMVC_rest</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring-mvc.xml</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>springMVC_rest</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```



#### 启动流程

1、解析<context-param>里的键值对。

2、创建一个application内置对象即ServletContext，servlet上下文，用于全局共享。

3、将<context-param>的键值对放入ServletContext即application中，Web应用内全局共享。

4、读取<listener>标签创建监听器，一般会使用ContextLoaderListener类，如果使用了ContextLoaderListener类，Spring就会创建一个WebApplicationContext类的对象，WebApplicationContext类就是IoC容器，ContextLoaderListener类创建的IoC容器是根IoC容器为全局性的，并将其放置在appication中，作为应用内全局共享，键名为WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE，可以通过以下两种方法获取。这个全局的根IoC容器只能获取到在该容器中创建的Bean不能访问到其他容器创建的Bean，也就是读取web.xml配置的contextConfigLocation参数的xml文件来创建对应的Bean。

```java
WebApplicationContext applicationContext = (WebApplicationContext) application.getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
WebApplicationContext applicationContext1 = WebApplicationContextUtils.getWebApplicationContext(application);
```

5、listener创建完成后如果有<filter>则会去创建filter。

6、初始化创建<servlet>，一般使用DispatchServlet类。

7、DispatchServlet的父类FrameworkServlet会重写其父类的initServletBean方法，并调用initWebApplicationContext()以及onRefresh()方法。

8、initWebApplicationContext()方法会创建一个当前servlet的一个IoC子容器，如果存在上述的全局WebApplicationContext则将其设置为父容器，如果不存在上述全局的则父容器为null。

9、读取<servlet>标签的<init-param>配置的xml文件并加载相关Bean。

10、onRefresh()方法创建Web应用相关组件。



### Spring MVC 运行流程

#### 流程示意图

![img](https://mubu.com/document_image/8a909857-4e3f-4763-af8e-e86ea865687b-983181.jpg)

#### Spring工作流程描述

1. 用户向服务器发送请求，请求被 Spring 前端控制器 DispatcherServlet 捕获
2. DispatcherServlet 对请求URL进行解析，得到请求资源标识符(URI)。然后根据该URI，调用HandlerMapping 获得该 Handler 配置的所有相关的对象（包括 Handler 对象以及 Handler 对象对应的拦截器），最后以 HandlerExecutionChain 对象的形式返回
3. DispatcherServlet 根据获得的 Handler，选择一个合适的 HandlerAdapter。（附注：如果成功获得 HandlerAdapter 后，此时将开始执行拦截器的 preHandler(…)方法）
4. 提取 Request 中的模型数据，填充 Handler入参，开始执行Handler（Controller)
5. Handler 执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象然后调用拦截器的 postHandle 方法
6. 根据返回的 ModelAndView，选择一个适合的 ViewResolver（必须是已经注册到 Spring 容器中的ViewResolver )返回给 DispatcherServlet 
7. ViewResolver 结合Model和View，来渲染视图
8. 将渲染结果返回给客户端
9. 执行拦截器的afterCompletion方法



### SpringMVC与Struts2区别

#### 框架机制

Struts2 采用 Filter（StrutsPrepareAndExecuteFilter）实现，SpringMVC（DispatcherServlet）则采用 Servlet 实现。

Filter 在容器启动之后即初始化；服务停止以后销毁，晚于 Servlet。

Servlet 在是在调用时初始化，先于 Filter 调用；服务停止后销毁。

#### 拦截机制

- Struts2
  - Struts2 框架是**类级别的拦截**，每次请求就会创建一个 Action，和 Spring 整合时Struts2的 ActionBean 注入作用域是原型模式 prototype（否则会出现线程并发问题），然后通过 setter ， getter 把 request 数据注入到属性。
  - Struts2 中，一个 Action 对应一个 request，response 上下文，在接收参数时，可以通过属性接收，这说明属性参数是让多个方法共享的。
  - Struts2 中 Action 的一个方法可以对应一个url，而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了
- SpringMVC
  - SpringMVC是**方法级别的拦截**，一个方法对应一个 Request 上下文，所以方法基本上是独立的，独享 request，response 数据。而每个方法同时又和一个 url 对应，参数的传递是直接注入到方法中的，是方法所独有的。处理结果通过 ModelMap 返回给框架。
  - 在 Spring 整合时，SpringMVC 的 Controller Bean 默认单例模式Singleton，所以默认对所有的请求，只会创建一个 Controller，因为没有共享的属性，所以是线程安全的，如果要改变默认的作用域，需要添加 @Scope 注解修改。

#### 性能方面

SpringMVC实现了零配置，由于 SpringMVC 基于方法的拦截，只要加载一次单例模式bean注入。

Struts2是类级别的拦截，每次请求对应实例一个新的Action，需要加载所有的属性值注入。

SpringMVC开发效率和性能高于Struts2。

#### 设计思想

Struts2 更加符合 OOP 的编程思想， SpringMVC 就比较谨慎，在 servlet 上扩展。

#### 集成方面

SpringMVC 集成了 Ajax，使用非常方便，只需一个注解 @ResponseBody 就可以实现，然后直接返回响应文本即可

而 Struts2 拦截器集成了 Ajax，在 Action 中处理时一般必须安装插件或者自己写代码集成进去，使用起来也相对不方便。



### Spring Boot

#### 什么是Spring Boot

Spring Boot 简化了基于 Spring 的应用开发，通过少量的代码就能创建一个独立的、产品级别的 Spring 应用。Spring Boot 依赖 Spring 框架来管理对象的依赖。

#### 主要目标

- 为所有Spring开发提供一个更快更广泛的入门体验；
- 开箱即用，不合适时也可以快速抛弃；
- 提供一系列大型项目常用的非功能性特征，比如：嵌入式服务器、安全性、度量、运行状况检查、外部化配置等；
- 零配置（无冗余代码生成和XML强制配置 ，遵循“约定大于配置”）；