---
title: Hexo博客完善
date: 2017-06-28 22:54:05
tags: [github,hexo,blog]
categories: 搭建博客
---

## 你的Blog得优化~
&nbsp;&nbsp;&nbsp;&nbsp;至此，我的博客已经建成，但是总觉得不够完美，还差一点，于是今天花了半天的时间在博客的优化上面，遇到了很多问题，自己一个一个去百度，按照其他博客的步骤来做，当然无法避免有些博客写的并不是很好，有些问题并没有很好的解决。
<!-- more -->
&nbsp;&nbsp;&nbsp;&nbsp;今天下午主要完成了三件事：
- 博客分类的完善，添加了博客分类，博客标签以及关于我。目的是为了后期更好的管理这些博客，对了还有搜索的功能，可以检索博客中的关键字词。
在博客目录下右键Git Bash执行命令:` $ hexo new pages tags `,在对应blog/source/下新建tags文件夹，用于标签的显示界面，此时你可能会问，为什么不自己直接建一个文件夹呀？ 当然你也可以自己建，只不过用命令的方式会默认在tags文件夹下建立一个`index.md`文件，改一个自己喜欢的title，并添加一行 ` type: "tags" `即可。如下图所示：

![addtags](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/addtags.jpg)

&nbsp;&nbsp;&nbsp;&nbsp;博客分类亦是如此，对应类型为` type: "cateagories"`。关于我并没有添加类型。
&nbsp;&nbsp;&nbsp;&nbsp;站点内容搜索功能：
- 安装hexo-generator-searchdb
对应博客目录下git bash执行：`$ npm install hexo-generator-searchdb --save`
站点配置文件_config.yml中添加search字段，如下：
```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```


- 第二是添加评论的功能：
 注册一个友言账号，[友言官网](http://www.uyan.cc/)，注册成功后进入后台管理获取uid，然后在站点配置文件_config.yml中找到`youyan_uid: `，附上刚刚的uid，最后部署一下网页即可。

- 最后就是添加站点阅读量，这个也是我最花时间的一件事情，开始尝试了百度统计，代码也安装成功，只不过访问数据得在百度统计上查看，需求是想在自己的网页footer位置添加一条网页阅读量的消息。尝试了好久，并没有找到好的方法。这里附上一个[推荐的教程](http://www.qingpingshan.com/jianzhan/cms/233565.html)，对于hexo博客的创建有详细的步骤~ 不说了，我也去加一个打赏的功能~




