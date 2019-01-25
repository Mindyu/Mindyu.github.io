---
title: Linux 学习笔记
date: 2019-1-5 22:01:41
tags: [CentOS]
categories: [Linux 学习]
---


### Centos虚拟机安装

#### 下载镜像

下载地址：https://www.centos.org/download/

点击 Minimal ISO ，选择其中一个可用的源，下载 Minimal 版本即可（最小化安装版本，只包含必须的软件包）

#### 安装

使用 VMware 安装Centos <!--more-->

1.选择语言进入安装目录
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/centos%E5%AE%89%E8%A3%85.png" width="50%" height="50%"/>

2.设置安装目录
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E5%AE%89%E8%A3%85%E4%BD%8D%E7%BD%AE.png"  width="50%" height="50%"/>

3.设置用户信息
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E7%94%A8%E6%88%B7%E4%BF%A1%E6%81%AF%E8%AE%BE%E7%BD%AE.png"  width="50%" height="50%"/>

4.设置root用户密码（提示密码等级弱，双击完成按钮即可）
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E8%AE%BE%E7%BD%AE%E5%AF%86%E7%A0%81.png"  width="50%" height="50%"/>

5.等待完成安装，重启
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E5%AE%8C%E6%88%90%E5%AE%89%E8%A3%85.png"  width="50%" height="50%"/>



### 准备工作

#### 查看IP

##### ip addr 命令

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/ipaddr.png "/>

此时IP地址就是网卡的 inet 的值，而上图第一个是本地服务地址，不是我们想要的。第二个没有 inet 这个属性值。

接下来使用vi编辑 /etc/sysconfig/network-scripts/ifcfg-XXX 配置网卡（XXX对应上图的ens33）

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E7%BD%91%E5%8D%A1%E9%85%8D%E7%BD%AE%E4%BF%AE%E6%94%B9.png"/>

修改ONBOOT值为 yes 。表示默认启动网卡。然后重启网络服务`service network restart `
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E6%9F%A5%E7%9C%8B%E5%86%85%E7%BD%91IP.png"/>

接下来就可以查看到IP地址了！



##### ifconfig 命令

使用该命令时，会提示`command not found`。我们需要使用`yum install net-tools`命令安装相应的服务。然后就可以使用 ifconfig 命令了！
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/ifconfig.png"  width="80%" height="80%"/>



#### 替换默认源

使用163源的帮助文档 http://mirrors.163.com/.help/centos.html

1. 首先备份/etc/yum.repos.d/CentOS-Base.repo 

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

2. 下载对应版本repo文件, 放入/etc/yum.repos.d/(操作前请做好相应备份) 

CentOS7: http://mirrors.163.com/.help/CentOS7-Base-163.repo

3. 运行以下命令生成缓存 

```
yum clean all
yum makecache
```
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E4%BF%AE%E6%94%B9%E9%BB%98%E8%AE%A4%E6%BA%90.png" width="80%" height="80%"/>



#### 安装 Vim

```
yum install vim
```





### SSH 工具

#### SSH服务端安装

对于服务器版本的系统默认是已经安装了ssh服务的。

```
# 安装SSH
yum install openssh-server
# 启动SSH
service sshd start
# 设置开机运行
chkconfig sshd on
```

#### SSH客户端安装

yum install openssh-clients

**连接ssh服务端**

ssh root@192.168.156.188

然后输入密码，即可连接到远程的SSH服务端。



**存在的问题**

Windows平台的Xshell连接服务器后提示`WARNING! The remote SSH server rejected X11 forwarding request.`警告

解决办法：

编辑 /etc/ssh/sshd_config。设置 X11Forwarding yes。如果依然无法解决则修改Xshell中当前连接的SSH->隧道(Tunneling)，然后关闭 X11转发 。
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/x11%E9%97%AE%E9%A2%98.png" width="50%" height="50%"/>



#### SSH config用法详解

config 用于方便我们管理多个SSH，存放路径为 ~/.ssh/config 。

**配置语法**：host 别名、 HostName 主机名、 User 用户名、Port 端口号、IdentityFile 秘钥文件（私钥）

配置完成之后，我们就可以使用` ssh 别名 `的方式访问服务器。

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/sshconfig.png">



#### 免密码登录方案之SSH Key

使用ssh工具生成公钥和私钥，然后在服务端进行注册，将生成的公钥复制到服务器中。然后就可以实现免密登录了。

Windows平台

通过 **Xshell -> 工具 -> 用户秘钥管理者 -> 生成 -> 设置秘钥类型和秘钥长度 -> 设置秘钥名称和秘钥加密的密码 -> 点击完成（另保存公钥）**

Linux平台

**进入.ssh目录 -> 使用 ssh-keygen -t rsa 命令 -> 设置秘钥名称和密码**

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/sshkey%E7%94%9F%E6%88%90.png"  width="80%" height="80%"/>

然后将（mindyu.pub）文件中公钥的复制到服务器端的 `~/.ssh/authorized_keys` 文件中去。

如果客户端是Linux平台，还需要将私钥进行加载 `ssh-add ~/.ssh/mindyu`。然后就可以免密访问。

而Windows平台只需要设置Xshell用户身份认证方式为`Public Key`即可。



#### SSH 端口安全

修改SSH默认的端口号

```
# 修改配置文件中 Port (可以监听多个端口)
vim /etc/ssh/sshd_config

# 重启SSH服务
service sshd restart
```



### Linux 常用命令

#### 软件操作命令

软件包管理器：yum

安装软件：yum install xxx

卸载软件：yum remove xxx

搜索软件：yum search xxx

清理缓存：yum clean packages

列出已安装：yum list 

软件包信息：yum info xxx



#### 服务器硬件资源和磁盘操作

内存：free -m

硬盘：df -h

负载：w 、top （Load Average 就是一段时间 (1 分钟、5分钟、15分钟) 内平均 Load ）

CPU：cat /proc/cpuinfo
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E7%A1%AC%E4%BB%B6%E8%B5%84%E6%BA%90%E4%BF%A1%E6%81%AF.png" width="80%" height="80%" />




#### 文件和文件夹操作





#### 系统用户操作命令





#### 防火墙相关设置





#### 文件传输





