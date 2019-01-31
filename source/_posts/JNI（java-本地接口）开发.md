---
title: JNI（java 本地接口）开发
date: 2017-07-02 18:31:24
tags: [NDK,JNI,安卓]
categories: [安卓学习]
---
### JNI开发之NDK环境搭建

​	首先得先说说JNI是什么，Java Native Interface(java本地接口)的缩写，主要是实现Java与其他语言的通信（主要是C，C++）。当实际项目中一些接口并不支持java安卓的时候，就得靠自己进行封装，来达到目的。缺点是java与本地以编译好的代码交互，可能会丧失平台可移植性，另外程序安全性降低使用不当可能是整个程序崩溃，使用时尽量降低语言之间的耦合性。<!--more-->开发JNI项目前提是需要有NDK（Native Development Kit）的支持。因此，在开发前需要先安装和配置NDK。推荐一个[NDK安装教程](http://blog.csdn.net/xbw12138/article/details/54882331#html) 对于windows系统依然适用。

1. 项目配置文件gradle.properties中添加

 ```
 android.useDeprecatedNdk=true
 ```
2. local.properties文件添加：（如果是在AS->SDK Manager->Appearance&Behavior->System Settings->Andriod SDK->SDK Tools安装，会自动配置）

 `ndk.dir=NDK的路径` 

3. 打开模块的build.gradle文件，在android/defaultConfig下面添加ndk节点，如下所示：

```
ndk {
    moduleName "JNIDemo"
    stl "stlport_static"
    ldLibs "log"
}
```
4. 创建JNIUtil类，声明本地方法。编译后在Terminal终端面板中定位到`cd app/build/intermediates/classes/debug/` 执行`javah 包名.JNIUtil`（JNIUtil是刚刚定义的类名）执行成功之后会在刚刚的debug目录下生成一个.h的头文件（文件名为:Java.类名.包名.h('.'换成'\_')）。
5. 在app目录下新建JNI目录，拷贝第四步生成的.h文件，再创建一个.cpp文件实现头文件中的方法。
6. ok~   编译运行。完美~

