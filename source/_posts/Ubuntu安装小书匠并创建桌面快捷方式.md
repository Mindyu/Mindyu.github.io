---
title: Ubuntu安装小书匠并创建桌面快捷方式
date: 2018-04-28 23:47:16
tags: [Ubuntu16.04,小书匠,Markdown]
categories: [Ubuntu学习]
---

## 小书匠简介
 [小书匠编辑器](http://soft.xiaoshujiang.com/)是一款专为markdown写作而设计的编辑器。 支持多种编辑模式。单栏编辑，双栏编辑，三栏编辑，实时预览，全屏写作，全屏阅读…想怎么切换，就怎么切换，就是这样随心所欲。 多种主题选择、丰富的语法支持、第三方同步等功能特色。
原先在windows上习惯了使用小书匠，然后就想着在Ubuntu上也装一个。<!--more-->
![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/%E5%B0%8F%E4%B9%A6%E5%8C%A0/xsj_stand.png)

## 小书匠安装
小书匠已经托管到github，首先下载对应版本到本地。
`https://github.com/suziwen/markdownxiaoshujiang`

解压下载下来的zip文件到指定文件夹：
`# unzip Story-writer-linux64.zip.zip -d /opt/Story-Writer/ `

切换到解压出来的文件夹根目录，然后启动软件
`# ./Story-writer`即可启动软件

![小书匠](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/%E5%B0%8F%E4%B9%A6%E5%8C%A0/story_writer.png)

## 存在的问题
但是存在两个问题：
1. 无快捷方式，每次打开过于麻烦
2. 不能设置小书匠为系统默认打开.md文件

## 创建小书匠桌面快捷方式
对于没有快捷方式这个问题，让我很容易想到当初安装Eclipse的时候也没有快捷启动方式。然后在网上找到解决方案。

1. UBuntu的菜单图标保存在/usr/share/applications目录下，所以先在该目录下建一个eclipse.desktop文件
`sudo gedit /usr/share/applications/eclipse.desktop`

2. 在文件中输入以下内容

``` stylus
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse Platfrom          //图标名字，可修改
Comment=Eclipse IDE			   //注释，可修改
Exec=/opt/eclipse/eclipse      //Eclipse文件夹下eclipse的路径
Icon=/opt/eclipse/icon.xpm     //Eclipse文件夹下图标icon.xpm的路径
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
```
3.对该文件进行赋权
`chmod u+x /usr/share/applications/eclipse.desktop`

4.双击eclipse的图标就可以运行，然后复制到桌面就可以了

根据Ecllipse图标的制作，同理，然后试了小书匠是否也可以。

``` stylus
[Desktop Entry]
Encoding=UTF-8
Name=Story_Writer          
Comment=Story_Writer
Exec=/opt/Story_Writer/Story-writer
Icon=/opt/Story_Writer/Story-writer.png
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
```
如图：
![小书匠图标](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/%E5%B0%8F%E4%B9%A6%E5%8C%A0/%E5%B0%8F%E4%B9%A6%E5%8C%A0.png)

然后就出现了一个Story_Writer的快捷方式，双击即可打开小书匠。是不是很简单～

 对于另一个问题目前还没找到解决方案....  原谅我还只是一个小白。如果有幸你可以看到，请留言我！
