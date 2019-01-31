---
title: Ubuntu16.04 常用软件安装
date: 2018-04-26 09:24:17
tags: [Ubuntu16.04,Linux命令]
categories: [Ubuntu学习]
---

## Ubuntu系统尝鲜
​	不知何时起，对Linux操作系统充满了好奇心，想去接触一番。	都说学编程搞开发的都离不开Linux操作系统，个人觉得Linux操作系统更适合学习，少了各种弹窗，界面更加清爽，系统任由你自己去操控，系统也更加稳定，许多的服务器也基本上都是Linux操作系统。然后就自己倒腾，在Windows上装了一个Ubuntu16.04LTS的双系统，通过日常是的使用去渐渐熟悉Linux操作系统，以及各种命令的使用～<!--more-->

## 常用软件安装

 1. [Shadowsocks安装](https://github.com/shadowsocks)

``` stylus
		sudo add-apt-repository ppa:hzwhuang/ss-qt5 

        sudo apt-get update

        sudo apt-get install shadowsocks-qt5
```

![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/shadowsocks.png)
	在Ubuntu系统上使用还需要设置系统代理，在 设置-》网络-》网络代理 中设置代理模式，最好设置为自动代理方式，然后添加一个pac文件，应用到整个系统即可。

2. 小书匠安装
一款Markdown编辑器，支持多平台使用。详细的[安装及添加桌面快捷方式](http://www.mindyu.com/2018/04/28/Ubuntu%E5%AE%89%E8%A3%85%E5%B0%8F%E4%B9%A6%E5%8C%A0%E5%B9%B6%E5%88%9B%E5%BB%BA%E6%A1%8C%E9%9D%A2%E5%BF%AB%E6%8D%B7%E6%96%B9%E5%BC%8F/)可见我的另一篇博客。

3. 安装搜狗输入法
其实个人感觉系统自带的输入法也还可以。
	3.1 在官网下载搜狗输入法安装包
	3.2 切换到deb包所在的目录，并执行以下命令： 
`sudo dkpg -i deb包名`
	3.3 若出现由于缺少相关依赖文件的错误。执行以下命令，安装所缺少的依赖文件，然后再次使用3.2命令完成安装： 
`sudo apt-get -f install`
	3.4 在“语言支持”面板，添加汉语支持，并将输入法系统设置为fcitx。然后重启
	3.5 在Dash中搜索fcitx configuartion，在Input Method中添加和配置sogoupinyin即可

4. JDK安装
 - 添加ppa
``` stylus
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
```
 - 安装oracle-java-installer


``` stylus
JDK8
sudo apt-get install oracle-java8-installer


JDK7
sudo apt-get install oracle-java7-installer
```
​	安装时会提示你同意Oracle的服务条款，选择ok，然后选择yes


5. Eclipse安装
安装Eclipse 
`sudo apt-get install eclipse`
启动Eclipse 
`eclipse`
以及快捷方式制作，可见[小书匠安装的详细博客](http://www.mindyu.com/2018/04/28/Ubuntu%E5%AE%89%E8%A3%85%E5%B0%8F%E4%B9%A6%E5%8C%A0%E5%B9%B6%E5%88%9B%E5%BB%BA%E6%A1%8C%E9%9D%A2%E5%BF%AB%E6%8D%B7%E6%96%B9%E5%BC%8F/)

6. MyEclipse安装
- 在[官方中文网](http://www.myeclipsecn.com/download/)下载MyEclipse安装包
- 我现在的是离线版，解压后为.run为后缀名的文件
- 双击执行，然后就和windows安装过程一样。选择安装目录比如/opt/MyEclipse/，一路Next即可。最后一步取消勾选。不立即打开MyEclipse。
- 然后下载对应的破解包，解压之后，执行cracker.jar包
`java -jar cracker.jar`
- 之后的步骤和Windows破解过程一样。
![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/myeclipse.png)
如果你一遍成功，那么就恭喜你。 我第一次安装失败了，因为破解包的问题，所以要确保破解包的正确性。 失败之后存在一个卸载不干净的问题。我也没有找到解决办法。 换了一台电脑，另外找了一个破解包，完美破解。

7. Google浏览器安装
- 下载 Chrome（Linux 版）https://www.google.cn/intl/zh-CN/chrome/

- 终端下输入如下内容来安装.deb文件
  `sudo dpkg -i 软件包名.deb`

- 然后在Dash中搜索Google，打开即可。

  使用中可能遇到两个问题：
   （1）重启之后，Google浏览器就打不开了
   （2）设置新标签页打开网页

  

   解决方案：
   问题1：
   网上的解决办法，大多是删除chrome的配置文件
   `rm -r~/.config/google-chrome `
   但是指标不治本，下次打开依然会出现问题
   真正的解决方案是：
   安装gnome-keyring。因为在登录谷歌账户之后，chrome会使用gnome-keyring来保护你的账户。
   `sudo apt install gnome-keyring`

  

   问题2：
   打开google页面，任意搜索一个内容，在搜索结果页面的上面靠中间位置有一个设置，点击设置然后选择 搜索设置 ，即进入如下页面。
   	![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/google%E6%89%93%E5%BC%80%E6%96%B9%E5%BC%8F.png)

  然后勾选结果打开方式的 在新的浏览器窗口中打开所选的每条搜索结果即可。

8. 系统监视器 

  实时查看电脑的cpu，内存占用率，更可以查看网速
  安装：
``` stylus
sudo add-apt-repository ppa:fossfreedom/indicator-sysmonitor 

sudo apt-get update  

sudo apt-get install indicator-sysmonitor  
```
![	](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/sysmonitor.png)

9. WPS 安装
- 在[WPS官网下载alpha版本](http://community.wps.cn/download/)，我下载的是wps-office_10.1.0.5672~a21_amd64.deb
- 执行安装命令：
`sudo dpkg -i wps-office_10.1.0.5672~a21_amd64.deb `
- 安装完成后，在Dash中即可搜索到，打开会发现字体缺失的问题
![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/wps_%E7%BC%BA%E5%A4%B1%E5%AD%97%E4%BD%93.png)
- 下载[字体包](http://pan.baidu.com/s/1mh0lcbY)
- 创建目录：
`sudo mkdir /usr/share/fonts/wps-office`
- 将下载的字体复制到创建的目录：
`sudo cp -r wps_symbol_fonts.zip /usr/share/fonts/wps-office`
- 切换到`/usr/share/fonts/wps-office`目录解压字体包：
`sudo unzip wps_symbol_fonts.zip`
- 解压后删除字体包：
`sudo rm -r wps_symbol_fonts.zip`
再次打开就不会提示缺失字体包了！
![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/wps.png)

10. 下载工具uget安装

``` stylus
sudo add-apt-repository ppa:plushuang-tw/uget-stable

sudo apt-get update

sudo apt-get install uget
```
![enter description here](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E8%BD%AF%E4%BB%B6/uget.png)
	终端和浏览器下载东西比较慢的时候可以试试，下载速度还是比较可观！



参考博客： 
https://www.cnblogs.com/wadxy1314/p/6880264.html

https://blog.csdn.net/Jesse_Mx/article/details/52816928

https://blog.csdn.net/u011324454/article/details/78497021

https://blog.csdn.net/VectorWWW/article/details/78820156

https://my.oschina.net/renwofei423/blog/635798