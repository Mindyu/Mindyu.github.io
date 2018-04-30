---
title: Ubuntu16.04 常用命令集合
date: 2018-04-28 13:27:17
tags: [Ubuntu16.04,Linux命令]
categories: [Ubuntu学习]
---

## 软件安装
方法一：

``` stylus
sudo apt-get install 软件名         //最常用方法
sudo apt-get remove 软件名			 //卸载软件
sudo apt-get update				   //更新软件列表，会访问源列表里的每个网址，并读取软件列表，然后保存在本地电脑
sudo apt-get upgrade			   //更新软件，把本地已安装的软件，与刚update软件进行对比，如果发现已安装的软件版本太低，就会提示你更新。
```
<!--more-->
方法二：
- deb软件包可直接安装

``` stylus
sudo  dpkg  -i  package.deb        //deb包安装方法
```
deb详解：

``` stylus
 dpkg -r package 			 删除包 
 dpkg -P package 			 删除包（包括配置文件）
 dpkg -L package 			 列出与该包关联的文件 
 dpkg -l packag 			 显示该包的版本e
 dpkg –unpack package.deb 	 解开 deb 包的内容 
 dpkg -S keyword 			 搜索所属的包内容 
 dpkg -l 					 列出当前已安装的包
 dpkg -c package.deb   		 列出 deb 包的内容 
 dpkg –configure package 	 配置包

```

方法三:
- rpm软件包安装
Ubuntu的软件包格式是deb，如果要安装rpm的包，则要先用alien把rpm转换成deb。

``` stylus
sudo apt-get install alien  //安装alien工具

alien -d *.rpm				//转换rpm包为deb包，然后再按方法二安装

alien -i *.rpm				//直接对rpm包进行安装
```

## 文件管理
- 常用命令：

``` stylus 
~                       		//当前用户主目录 
cd ~                    		//home目录（/开头是绝对路径；.开头是相对路径）
pwd                     		//获取当前路径
mkdir mydir             		//新建目录mydir
cp test(此处可为路径) /opt/test   //将当前目录下的test复制到test中
rm                      		//删除文件
mv  oldname newname            	//重命名
remove 'y/a-z/A-Z/' *.c  		//删文件
cat test                		//查看文件test;-n显示行号
file test               		//查看文件类型
ls                         		//查看当前目录下的文件
ls -l 文件名称              	 //查看详细信息（文件夹将-l改为-ld）

```

- 文件权限说明：
``` stylus
// 文件属性 连接数 文件拥有者 所属群组 文件大小 文件修改时间 文件名
drwxrwxr-x  9 mindyu mindyu 4096 4月  29 01:15 Blog
```
![文件详细权限](http://hexoblog-1253306922.cosgz.myqcloud.com/photo2018/ubuntu%E5%91%BD%E4%BB%A4/ls-l.png)
r可读，w可写，x 可执行，-不可读/写/执行
文件属性占10个位置
例如：
d rwx rwx r-x
第一个字符指定了文件类型：
‘- ’ 代表非目录的文件
‘d’ 代表一个目录。
第二段是文件拥有者User的属性，
第三段是文件所属群组Group的属性，
第四段是对于其它用户Other的属性。

- 权限修改
方法一：

``` stylus
//chmod [-R] 模式 文件
chmod abc file
```
其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。r=4，w=2，x=1

例子：
若要rwx属性则4+2+1=7；
若要rw-属性则4+2=6；
若要r-x属性则4+1=5。

``` stylus
sudo chmod 600 ××× （只有所有者有读和写的权限）
sudo chmod 644 ××× （所有者有读和写的权限，组用户和其他用户只有读的权限）
sudo chmod 777 ××× （每个人都有读和写以及执行的权限）
```

方法二：

``` stylus
chmod [ugoa] {+|-|=} [rwxst] 文件
```
用户参数：u 文件主	 g 同组用户 	o 其他用户 	a 所有用户 
操作方法：\+ 增加后列权限 	\- 取消后列权限 	= 置成后列权限 
操作参数：r 可读 	w 可写 	x 可执行 	s 运行时可置UID 	t 运行时可置GID 

``` stylus
chmod u+rw abc.txt      	//给用户增加读写权限
chmod o-rwx abc.txt         //不允许其他用户读写执行
chmod g=rx abc.txt          //只允许群组读取和执行
```

## apt-get常用命令
  update - 取回更新的软件包列表信息
  upgrade - 进行一次升级
  install - 安装新的软件包(注：软件包名称是 libc6 而非 libc6.deb)
  remove - 卸载软件包
  purge - 卸载并清除软件包的配置
  autoremove - 卸载所有自动安装且不再使用的软件包
  dist-upgrade - 发布版升级，见 apt-get(8)
  dselect-upgrade - 根据 dselect 的选择来进行升级
  build-dep - 为源码包配置所需的编译依赖关系
  clean - 删除所有已下载的包文件
  autoclean - 删除已下载的旧包文件
  check - 核对以确认系统的依赖关系的完整性
  source - 下载源码包文件
  download - 下载指定的二进制包到当前目录
  changelog - 下载指定软件包，并显示其changelog

## 解压缩命令
.tar
解包：`tar xvf FileName.tar`
打包：`tar cvf FileName.tar DirName` 将DirName文件夹打包成一个FileName.tar文件
打包：将一大堆文件或目录变成一个总的文件，
压缩：将一个大的文件通过一些压缩算法变成一个小文件。

.gz
解压1：`gunzip FileName.gz`
解压2：`gzip -d FileName.gz`
压缩：`gzip FileName`

.tar.gz 和 .tgz
解压：`tar zxvf FileName.tar.gz`
压缩：`tar zcvf FileName.tar.gz DirName`

.bz2
解压1：`bzip2 -d FileName.bz2`
解压2：`bunzip2 FileName.bz2`
压缩： `bzip2 -z FileName`

.tar.bz2
解压：`tar jxvf FileName.tar.bz2`
压缩：`tar jcvf FileName.tar.bz2 DirName`

.bz
解压1：`bzip2 -d FileName.bz`
解压2：`bunzip2 FileName.bz`
压缩：未知

.tar.bz
解压：`tar jxvf FileName.tar.bz`
压缩：未知

.Z
解压：`uncompress FileName.Z`
压缩：`compress FileName`

.tar.Z
解压：`tar Zxvf FileName.tar.Z`
压缩：`tar Zcvf FileName.tar.Z DirName`

.zip
解压：`unzip FileName.zip`
压缩：`zip FileName.zip DirName`

.rar
解压：`rar x FileName.rar`
压缩：`rar a -r FileName.rar DirName`
解压需要安装：`sudo apt-get install unrar`
压缩需要安装：`sudo apt-get install rar`

