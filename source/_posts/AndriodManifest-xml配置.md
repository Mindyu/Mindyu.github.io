---
title: AndriodManifest.xml配置
date: 2017-07-19 20:40:36
tags: [andriod,安卓]
categories: [安卓学习]
---

### AndriodManifest的配置基础
1.全局篇
应用的包名以及版本信息，控制安卓的版本信息。
<center>

![andriodmanifest.xml][1]

</center>
<!-- more -->
2.组件篇（四大组件）
    Activity(活动)
    ：启动一个没有在清单中定义的Activity会抛出异常
    Service(服务)
    ：做为后台运行的一个逻辑代码的处理
    Content Provider(内容提供者)
    ：是用来管理数据库访问以及程序内和程序间共享的
    Broadcast Receiver(广播接收者)

 其属性可以设置：
图标：android:icon
标题：android:label
主题样式：android:theme
注意：只能包含一个application节点


3.权限篇
<uses-permission>申请系统权限
自定义权限，限制其它应用使用该应用的效果。
```
<permission
        andriod:name="com.example.test"
        andriod:protectionLevel="normal">
</permission>

<activity
        andriod:permission="com.example.test"
        ....
        >

```
然后在其他应用中若希望调用该应用则必须声明权限：
```
<uses-permission andriod:name="com.example.test">
```

### ListView的使用

数据适配器：
![数据适配器作用][2]
纯文字使用ArrayAdapter,有图片有文字又复杂的一系列内容的用SimpleAdapter.
1.ArrayAdapter(context, 当前listview加载的每一个列表项所对应的布局文件，数据源)
2.SimpleAdapter( context, data , resource , from ,to )：
context：上下文
data：数据源（List<? extends Map<String,?>> data）一个由Map组成的List合集
	每一个Map都对应ListView列表中的一行
	每一个Map（键-值对）中的键都必须包含所有在from中所指定的键
resource：列表项的布局文件ID
from：Map中的键名
to：绑定数据视图中的ID，与from成对应关系。
监听器：
![ListView的两种监听器][3]


  [1]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/andriodmanifest.png
  [2]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E6%95%B0%E6%8D%AE%E9%80%82%E9%85%8D%E5%99%A8.png
  [3]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E7%9B%91%E5%90%AC%E5%99%A8.png