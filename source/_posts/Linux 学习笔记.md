---
title: Linux 学习笔记
date: 2019-1-15 21:01:41
tags: [CentOS]
categories: [Linux 学习]
---


### Centos虚拟机安装

#### 下载镜像

​	下载地址：https://www.centos.org/download/

​	点击 Minimal ISO ，选择其中一个可用的源，下载 Minimal 版本即可（最小化安装版本，只包含必须的软件包）

#### 安装

​	使用 VMware 安装Centos <!--more-->

1. 选择语言进入安装目录

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/centos%E5%AE%89%E8%A3%85.png" width="50%" height="50%"/>

2. 选择安装目录

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E5%AE%89%E8%A3%85%E4%BD%8D%E7%BD%AE.png"  width="50%" height="50%"/>

3. 设置用户信息

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E7%94%A8%E6%88%B7%E4%BF%A1%E6%81%AF%E8%AE%BE%E7%BD%AE.png"  width="50%" height="50%"/>

4. 设置root用户密码（提示密码等级弱，双击完成按钮即可）

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E8%AE%BE%E7%BD%AE%E5%AF%86%E7%A0%81.png"  width="50%" height="50%"/>

5. 等待完成安装，重启

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E5%AE%8C%E6%88%90%E5%AE%89%E8%A3%85.png"  width="50%" height="50%"/>



### 准备工作

#### 查看IP

##### ip addr 命令

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/ipaddr.png "/>

​	此时IP地址就是网卡的 inet 的值，而上图第一个是本地服务地址，不是我们想要的。第二个没有 inet 这个属性值。

​	接下来使用vi编辑 /etc/sysconfig/network-scripts/ifcfg-XXX 配置网卡（XXX对应上图的ens33）

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E7%BD%91%E5%8D%A1%E9%85%8D%E7%BD%AE%E4%BF%AE%E6%94%B9.png"/>

​	修改ONBOOT值为 yes 。表示默认启动网卡。然后重启网络服务`service network restart `
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E6%9F%A5%E7%9C%8B%E5%86%85%E7%BD%91IP.png"/>

​	接下来就可以查看到IP地址了！



##### ifconfig 命令

​	使用该命令时，会提示`command not found`。我们需要使用`yum install net-tools`命令安装相应的服务。然后就可以使用 ifconfig 命令了！
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/ifconfig.png"  width="80%" height="80%"/>



#### 替换默认源

​	使用163源的帮助文档 http://mirrors.163.com/.help/centos.html

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

​	对于服务器版本的系统默认是已经安装了ssh服务的。

```
# 安装SSH
yum install openssh-server
# 启动SSH
service sshd start
# 设置开机运行
chkconfig sshd on
```

#### SSH客户端安装

​	yum install openssh-clients

**连接ssh服务端**

​	ssh root@192.168.156.188

​	然后输入密码，即可连接到远程的SSH服务端。



**存在的问题**

​	Windows平台的Xshell连接服务器后提示`WARNING! The remote SSH server rejected X11 forwarding request.`警告

**解决办法**：

​	编辑 /etc/ssh/sshd_config。设置 X11Forwarding yes。如果依然无法解决则修改Xshell中当前连接的SSH->隧道(Tunneling)，然后关闭 X11转发 。
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/x11%E9%97%AE%E9%A2%98.png" width="50%" height="50%"/>



#### SSH config用法详解

​	config 用于方便我们管理多个SSH，存放路径为 ~/.ssh/config 。

​	**配置语法**：host 别名、 HostName 主机名、 User 用户名、Port 端口号、IdentityFile 秘钥文件（私钥）

​	配置完成之后，我们就可以使用` ssh 别名 `的方式访问服务器。

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/sshconfig.png">



#### 免密码登录方案之SSH Key

​	使用ssh工具生成公钥和私钥，然后在服务端进行注册，将生成的公钥复制到服务器中。然后就可以实现免密登录了。

Windows平台

​	通过 **Xshell -> 工具 -> 用户秘钥管理者 -> 生成 -> 设置秘钥类型和秘钥长度 -> 设置秘钥名称和秘钥加密的密码 -> 点击完成（另保存公钥）**

Linux平台

​	**进入.ssh目录 -> 使用 ssh-keygen -t rsa 命令 -> 设置秘钥名称和密码**

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/sshkey%E7%94%9F%E6%88%90.png"  width="80%" height="80%"/>

​	然后将（mindyu.pub）文件中公钥的复制到服务器端的 `~/.ssh/authorized_keys` 文件中去。

​	如果客户端是Linux平台，还需要将私钥进行加载 `ssh-add ~/.ssh/mindyu`。然后就可以免密访问。而Windows平台只需要设置Xshell用户身份认证方式为`Public Key`即可。



#### SSH 端口安全

​	修改SSH默认的端口号

```
# 修改配置文件中 Port (可以监听多个端口)
vim /etc/ssh/sshd_config

# 重启SSH服务
service sshd restart
```



### Linux 常用命令

#### 软件操作命令

> 软件包管理器：yum
>
> 安装软件：yum install xxx
>
> 卸载软件：yum remove xxx
>
> 搜索软件：yum search xxx
>
> 清理缓存：yum clean packages
>
> 列出已安装：yum list 
>
> 软件包信息：yum info xxx
>



#### 服务器硬件资源和磁盘操作

> 内存：free -m
>
> 硬盘：df -h
>
> 负载：w 、top （Load Average 就是一段时间 (1 分钟、5分钟、15分钟) 内平均 Load ）
>
> CPU：cat /proc/cpuinfo

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/%E7%A1%AC%E4%BB%B6%E8%B5%84%E6%BA%90%E4%BF%A1%E6%81%AF.png" width="80%" height="80%" />



#### 文件和文件夹操作

##### Linux 文件目录结构

![Linux 文件目录结构](https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/linux%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png )



##### 文件基本操作

|  命令   |          解释           | 命令  |   解释   |
| :-----: | :---------------------: | :---: | :------: |
| ls / ll |     查看目录下文件      | touch | 新建文件 |
|  mkdir  | 创建文件夹(-p 逐层创建) |  cd   | 进入目录 |
|   rm    | 删除文件和目录(-r 循环) |  cp   |   复制   |
|   mv    |          移动           |  pwd  | 显示路径 |



##### 文件编辑神器 Vim

​	快捷键键盘图：

![键盘图](http://www.runoob.com/wp-content/uploads/2015/10/vi-vim-cheat-sheet-sch.gif)

​	工作模式：

<img src="http://www.runoob.com/wp-content/uploads/2014/07/vim-vi-workmodel.png" width="50%" height="50%" />

​	[Linux vi/vim | 菜鸟教程](http://www.runoob.com/linux/linux-vim.html)



##### 文件权限421

​	rwx （读4、写2、可执行1）

```
drwxr-xr-x    // 表示当前为文件夹，创建者权限为rwx，用户组权限为r-x，其他用户权限为r-x
```



##### 文件搜索、查找、读取

| 命令 |      解释      | 命令 |    解释    |
| :--: | :------------: | :--: | :--------: |
| tail | 从文件尾开始读 | head | 从文件头读 |
| cat  |  读取整个文件  | more |  分页读取  |
| less |    可控分页    | grep | 搜索关键字 |
| find |    查找文件    |  wc  |  统计个数  |



##### 文件的压缩和解压

**tar 命令**
	以下5个独立命令，解压缩时只能用到其中一个。
```
-c: 建立压缩档案 
-x：解压 
-t：查看内容 
-r：向压缩归档文件末尾追加文件 
-u：更新原压缩包中的文件
```

​	下面的参数是根据需要在压缩或解压档案时可选的。
```
-z：有gzip属性的 
-j：有bz2属性的 
-Z：有compress属性的 
-v：显示所有过程 
-O：将文件解开到标准输出 
```

​	-f 命令为**必选**的命令，后面添加档案名

```
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。 
```

 [Linux tar.gz、tar、bz2、zip 等解压缩、压缩命令详解](http://www.runoob.com/w3cnote/linux-tar-gz.html)



#### 系统用户操作命令

| 命令    | 解释                          |
| ------- | ----------------------------- |
| useradd | 添加用户                      |
| adduser | 添加用户                      |
| userdel | 删除用户(-r 表示删除用户目录) |
| passwd  | 设置密码                      |

​	在 CentOS 中，useradd和adduser是一样的。都是在创建了用户之后，会在/etc/passwd文件中加一条新建用户的记录，然后在/home目录下创建新用户的主目录，并把/etc/skel目录中的文件复制到这个主目录下面。 **注意：这种方法创建的新用户，在设置密码之前是不能登陆到系统上的，需要在root权限下使用“passwd 用户名”的方法为指定的用户设置密码。下次才能以该用户名和密码登陆到系统中。** 

​	在 Ubuntu 中，推荐使用adduser命令，adduser命令会创建用户，同时创建同名的组，添加用户到对应的组中，创建对应的home文件夹，拷贝/etc/skel文件，最后输入用户密码。而useradd还需要添加其他参数。



#### 防火墙相关设置

**安装及使用**

> 安装：yum install firewalld
>
> 启动：service firewalld start 
>
> 检查状态：service firewalld status 
>
> 关闭或禁用防火墙：service firewalld stop/disable

**配置 firewalld-cmd**

> 查看版本： firewall-cmd --version
>
> 查看帮助： firewall-cmd --help
>
> 显示状态： firewall-cmd –state
>
> 列出所有的区域：firewall-cmd --get-zones
>
> 列出默认区域：firewall-cmd --get-default-zone
>
> 列出所有区域配置： firewall-cmd --list-all-zone
>
> 查看所有打开的端口： firewall-cmd --zone=public --list-ports
>
> 查看规则：iptables -L -n
>
> 更新防火墙规则： firewall-cmd --reload
>
> 查看区域信息:  firewall-cmd --get-active-zones
>
> 查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0

**添加服务** 

> firewall-cmd –add-service=ssh
>
> firewall-cmd –query-service=ssh
>
> firewall-cmd –remove-service=ssh

**添加端口**

> 添加：firewall-cmd --zone=public --add-port=80/tcp 
>
> 重新载入：firewall-cmd --reload
>
> 查看：firewall-cmd --zone=public --query-port=80/tcp
>
> 删除：firewall-cmd --zone=public --remove-port=80/tcp



#### 提权和文件上传和下载

​	使用普通用户时，当我们安装软件等操作时，就会提示权限不够，此时就需要提权操作。但是当我们使用 sudo 进行提权时，会提示 `xxx用户 is not in the sudoers file.  This incident will be reported.`即当前用户不在 sudoers 文件中。

​	那么就需要使用 root 用户登录然后通过 visudo 命令添加。

```
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL
%mindyu ALL=(ALL)       ALL		# 允许mindyu用户使用提权操作
```

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/visudo.png" width="80%" height="80%"/>



**服务器端下载命令**

```
	wget http://www.baidu.com

	curl -o baidu.html http://www.baidu.com		// -o 指定文件名
```



**本地上传命令**

对于 Linux 平台：

```
# 上传本地 test.txt 文件到服务器的 tmp 目录下
scp test.txt root@192.168.156.188:/tmp/

# 下载服务器的 test.txt 文件到本地当前目录
scp root@192.168.156.188:/tmp/test.txt ./
```

对于 Windows 平台：

​	使用 Xshell 软件即可。首先需要在服务器端安装 `yum install lrzsz`，然后就可以使用一下命令：

```
# 上传，回车之后选择需上传的文件
rz 

# 下载，xxx表示文件名，回车之后选择下载的路径
sz xxx
```

​	另外 WinSCP 软件可以实现可视化的文件上传下载功能。EditPlus 软件可以实现修改远程服务器配置文件。



### WebServer 安装和配置

#### Apache

**基本操作**

| 解释 | 命令                |
| ---- | ------------------- |
| 安装 | yum install httpd   |
| 启动 | service httpd start |
| 停止 | service httpd stop  |

**虚拟主机配置**

​	配置 /etc/httpd/conf/httpd.conf 文件

```xml
<VirtualHost *:80>
    ServerName www.mindyu.test
    DocumentRoot /data/www
    <Directory "/data/www">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

​	在本地需要修改 host 文件，添加 www.mindyu.test 的映射。Windows 平台在 `C:\WINDOWS\system32\drivers\etc` 路径下。

​	如果配置完成之后，访问该网址时，依然进入的是 Apache的默认页，则  `sudo setenforce 0`即可！

同时可以通过修改默认配置 `/etc/selinux/config `文件中的 SELINUX 值为 disabled 。



**伪静态**

​	通过 rewrite 模块实现

```xml
<VirtualHost *:80>
    ServerName www.mindyu.test
    DocumentRoot /data/www
    <Directory "/data/www">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
        <IfModule mod_rewrite.c>
            RewriteEngine On
            RewriteRule ^(.*).htmp$ index.html    # 将所有 .htmp 请求重写到 index.html
        </IfModule>
    </Directory>
</VirtualHost>
```



#### Nginx

**安装**

1. 添加源

```
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

2. 安装

```
sudo yum install -y nginx
```

3. 使用

```
# 启动
sudo service nginx start
# 重载
sudo service nginx reload
# 停止
sudo service nginx stop
# 开机启动
sudo systemctl enable nginx.service
```

**配置一个虚拟主机**

​	在 /etc/nginx/conf.d/ 目录下，新建一个 test.conf 的文件进行以下配置。

```
server {
    listen       80;
    server_name  www.mindyu.test;
    root   /data/www;
    index  index.html index.htm;
}
```

​	配置完成之后，进行重载即可访问。

*注：当 Apache 服务启动时，就无法在启动 nginx 服务了，因为它们默认都是 80 端口。*



**伪静态**

```
server {
    listen       80;
    server_name  www.mindyu.test;
    root   /data/www;
    index  index.html index.htm;
    location / {
        rewrite ^(.*)\.htmp$ /index.html;
    }
}
```



**日志记录**

​	nginx 可进行日志记录，通过配置 nginx.conf 文件

```
	# 格式化样式
	log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
                      
	# 日志路径和采用的格式化方式
    access_log  /var/log/nginx/access.log  main;
```

​	另外也可以在每个不同的虚拟主机中进行单独配置，实现不同的应用生成不同的日志文件。



**反向代理和负载均衡**

```
upstream test_hosts{
    server 185.199.111.153:80 weight=5;
    server 192.168.156.188:80 weight=1;
}

server {
    listen       80;
    server_name  www.mindyu.test;
    root   /data/www;
    index  index.html index.htm;
    location / {
        # rewrite ^(.*)\.htmp$ /index.html;
        proxy_pass http://test_hosts;
    }    
}
```



### MySQL 数据库服务

#### 安装

​	CentOS 7 默认安装 mariadb。在安装 MySQL 之前需要先卸载该软件。通过搜索命令 `yum search mysql`，即可查找到 mariadb 的安装情况。然后卸载 `sudo yum remove mariadb-libs.x86_64`。接下来就是安装 MySQL 的过程了。

1. 首先在[ MySQL 官网](https://dev.mysql.com/downloads/repo/yum/)下载源

```
wget https://dev.mysql.com/get/mysql80-community-release-el7-2.noarch.rpm
```

2. 安装源（安装完成之后就可以通过 yum search mysql 查找到 mysql 了）

```
sudo yum localinstall mysql80-community-release-el7-2.noarch.rpm 
```

3. 安装 MySQL

```
sudo yum install mysql-community-server.x86_64
```

#### 基本操作

> 启动：sudo service mysqld start
>
> 停止：sudo service mysqld stop
>
> 重启：sudo service mysqld restart
>
> 查看mysql进程：ps -ef | grep mysql



​	服务启动之后可以使用 `cat /var/log/mysqld.log | grep password` 命令查看临时密码，拿到临时密码之后进行登录 `mysql -uroot -pXXXXXXXXXX` 。登录之后可以看到提示 `mysql: [Warning] Using a password on the command line interface can be insecure.`表示该密码不安全需要修改密码。

​	修改密码命令：`ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';`。但是由于密码设置的过于简单会提示 `ERROR 1819 (HY000): Your password does not satisfy the current policy requirements`。此时需要进一步修改密码策略和密码长度限制。

​	MySQL 5.7：

```
set global validate_password_policy=0;
set global validate_password_length=1;
```

​	MySQL 8.0：

```
set global validate_password.policy=0;
set global validate_password.length=1;
```



#### 设置远程访问

```
show databases;
user mysql;
select Host,User from user \G;

# % 表示所有主机都可以访问
update user set host='%' where Host='localhost' and User='root';

# 刷新权限
flush privileges;
```

​	修改后：
<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/user%E8%A1%A8%E4%BF%A1%E6%81%AF.png" />


​	但是使用 MySQL 8.0 时，当我们使用本地的可视化软件 SQLyog 连接时，提示 1251 错误。

<img src="https://hexoblog-1253306922.cos.ap-guangzhou.myqcloud.com/photo2019/%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8BLinux%20%E7%8E%A9%E8%BD%AC%E5%85%B8%E5%9E%8B%E5%BA%94%E7%94%A8/1251%E8%BF%9E%E6%8E%A5%E9%94%99%E8%AF%AF.png" />

**解决方案**

```
# 修改mysql_native_passwd密码
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

# 然后刷新权限
flush privileges;
```



#### 开启 genolog 

​	可以记录所有执行的 sql 语句。

```
# 设置general_log的路径
set global general_log_file="/tmp/general.log";

# 开启general_log
set global general_log=on;
```



#### 创建用户

```
# 创建新用户 mindyu。任何主机都可访问，密码为 123456
create user 'mindyu'@'%' identified by '123456';

# 赋予权限 *.* 表示所有库所有表
 grant all privileges on *.* to 'mindyu'@'%'; 
 
# 刷新权限
flush privileges;
```

​	赋予权限： grant  to

​	收回权限：revoke from



#### 忘记密码时如何找回

​	在 /etc/my.cnf 配置文件中加入 skip-grant-tables。然后重启 MySQL 服务，即可无密码登录服务。

```
# 首先刷新权限
flush privileges;

# 修改密码
use mysql;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

# 如果提示密码不安全则设置密码策略，再修改密码
set global validate_password.policy=0;
set global validate_password.length=1;

# 再刷新权限即可
flush privileges;
```



### 缓存服务

#### memcached

```
安装
sudo yum install memcached  

启动 -d：后台启动 -l:监听IP地址 -m:分配内存大小 -p：端口号
memcached -d -l -m -p 

停止
kill pid
```

**telnet 命令**

```
# 安装
yum install telnet.*

# 检测端口是否连通（quit退出）
telnet 127.0.0.1 11211
```



#### Redis

```
# 获取源码（官网的下载链接）
wget http://download.redis.io/releases/redis-5.0.3.tar.gz

# 解压
tar zxvf redis-5.0.3.tar.gz

# 此时编译时会提示 没有 gcc 命令，所以先安装 gcc
yum install gcc

# 再次 make 时，仍然报错 fatal error: jemalloc/jemalloc.h: No such file or directory
make MALLOC=libc

# 执行安装
sudo make install
```





### Java Web环境配置

#### Jdk 安装

```
# 安装 openjdk  -y 表示自动选择确认 
sudo yum -y install java-1.8.0-openjdk
```



#### Tomcat 安装

```
# 下载（官网）
wget http://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.gz

# 解压
tar zxvf apache-tomcat-8.5.37.tar.gz

# 重命名
mv apache-tomcat-8.5.37 tomcat

# 启动
./tomcat/bin/startup.sh

# 关闭
./tomcat/bin/shutdown.sh
```

​	此时就可以使用 ip:8080 访问tomcat服务器了，我们可以将Tomcat和nginx结合起来，通过域名的方式直接访问8080端口tomcat服务器。

​	进入 /etc/nginx/conf.d 目录下，新建一个配置文件，将80端口的请求转发给8080端口的Tomcat服务器。

```
server {
    listen       80;
    server_name  www.java.test;
    location / {
        proxy_pass http://127.0.0.1:8080;
    }    
}
```



#### Maven 安装

```
# 下载
wget http://mirrors.shu.edu.cn/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz

# 解压
tar zxvf apache-maven-3.6.0-bin.tar.gz

# 重命名
mv apache-maven-3.6.0 maven

# 添加软链（这样就可以在命令行使用 mvn 命令）
sudo ln -s ~/maven/bin/mvn /usr/bin/mvn
```



### Python 环境配置

#### pip 安装

​	python2.7.5版本中，python目录下没有Scripts这个文件夹 ，导致一些指令使用不了，`yum install python-pip` 无法安装。 

​	**解决方案**：

1. 安装 setuptools ，进入官网下载最新版本的 [setuptools ](https://pypi.org/project/setuptools/)，然后解压，进入目录进行安装`python setup.py install`。
2. 安装 pip , 下载最新版本的 [pip](https://pypi.org/project/pip/)，然后解压，进入目录进行安装`python setup.py install`。



#### pip 替换豆瓣源

1. 创建文件夹

mkdir ~/.pip

2. 编辑配置文件

 vim ~/.pip/pip.conf

```
[global]
timeout=60
index-url=http://mirrors.aliyun.com/pypi/simple/
```



#### python 虚拟环境

​	virtualenv 用来为一个应用创建一套“隔离”的Python运行环境 

```
# 安装
sudo pip install virtualenv

# 创建一个隔离的 python 环境（--no-site-packages 表示不携带系统已安装的第三方包）
virtualenv pyvenv

# 进入环境
source pyvenv/bin/activate

# 退出环境
deactivate
```





### 服务管理

#### crontab 定时任务

​	linux 系统中的计划性任务就是由 cron (crond) 这个系统服务来控制的。该系统服务是默认启动的。另外, 由于使用者自己也可以设置计划任务，所以 Linux 系统也提供了使用者控制计划任务的命令：crontab 命令。 

​	crond 是linux下用来周期性的执行某种任务或等待处理某些事件的一个守护进程，该进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务。 

​	Linux下的任务调度分为两类，**系统任务调度**和**用户任务调度**。

​	系统任务调度：系统周期性所要执行的工作，比如写缓存数据到硬盘、日志清理等。 在/etc目录下有一个crontab文件，这个就是系统任务调度的配置文件。 

```
# crontab 文件
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

​	用户任务调度：用户定期要执行的工作，比如用户数据备份、定时邮件提醒等。用户可以使用 crontab 工具来定制自己的计划任务。所有用户定义的crontab 文件都被保存在 /var/spool/cron 目录中。 



#### Ntpdata 日期同步

​	Linux 系统的时间经常和当前时间有区别。可以使用 ntpdate 命令来设置系统时间。

```
# 安装
sudo yum install ntpdate

# 同步时间
ntpdate cn.pool.ntp.org

# 查看系统时间
date

# 查看软链（对应时区信息）
ll /etc/localtime

# 删除软链
rm /etc/localtime 

# 新建上海时区的软链即可
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

**定时任务**

```
# 新建定时任务
crontab -e 

# 添加更新任务
ntpdate cn.pool.ntp.org

# 查看定时任务
crontab -l
```



#### Logratate 日志切割

​	Logratate 是Linux系统自带的日志切割管理工具，可以自动进行日志的分割、压缩、覆盖等操作，同时可以设置按时间（天、周、月）、日志大小来执行分割操作。

​	系统的配置文件在 /etc/logrotate.d 目录下和/etc/logrotate.conf。

​	比如 /etc/logrotate.d/nginx 文件：

```
/var/log/nginx/*.log {
        daily		# 按天分割
        missingok	# 在日志轮循期间，任何错误将被忽略，例如“文件无法找到”之类的错误
        rotate 52	# 一次最多存储52个归档日志
        compress	# 在轮循任务完成后，已轮循的归档将使用gzip进行压缩
        delaycompress	# 最近的日志不归档
        notifempty		# 如果日志文件为空，轮循不会进行
        create 640 nginx adm	# 以指定的权限创建全新的日志文件
        sharedscripts	
        postrotate				# 脚本命令
                if [ -f /var/run/nginx.pid ]; then
                        kill -USR1 `cat /var/run/nginx.pid`
                fi
        endscript
}
```

​	sharedscripts：运行postrotate脚本，作用是在所有日志都轮转后统一执行一次脚本。如果没有配置这个，那么每个日志轮转后都会执行一次脚本

​	dateext：使用当期日期作为命名格式 

​	dateformat .%s：配合dateext使用，紧跟在下一行出现，定义文件切割后的文件名，必须配合dateext使用，只支持 %Y %m %d %s 这四个参数 

​	[运维中的日志切割操作梳理](https://cloud.tencent.com/developer/article/1027098)



#### supervisor 进程管理

​	supervisor 是一个 Linux 上用来管理程序后台运行的工具，支持程序的自启动，挂掉重启，日志，查看服务状态等功能。可配置程序随系统启动，并支持挂掉重启，增强程序稳定性。 

**安装及配置**

```
# 安装
yum install supervisor 

# 创建配置文件夹
mkdir /etc/supervisor

# 生成配置文件到 supervisor 目录
echo_supervisord_conf > /etc/supervisor/supervisord.conf

# 修改配置文件，在文件尾添加
[include]
files = /etc/supervisor/conf.d/*.ini

# 创建 conf.d 文件夹存放各个服务的配置
mkdir conf.d

# 创建 redis 服务配置
touch redis.ini

# 编辑服务配置
[program:redis]
command=/usr/local/bin/redis-server
autostart=true
autorestart=true
startsecs=3

# 启动
supervisord -c /etc/supervisor/supervisord.conf 

# 客户端工具查看服务启动状态
supervisorctl -c /etc/supervisor/supervisord.conf
```



**supervisor 开机自启**

```
# 编辑启动文件
vim /etc/rc.local

# 在新行添加要执行的命令
supervisord -c /etc/supervisor/supervisord.conf 
```



**常用命令**

```
# 查看所有 action
supervisorctl help

# 控制所有进程
supervisorctl start all
supervisorctl stop all
supervisorctl restart all

# 控制目标进程
supervisorctl stop redis
supervisorctl start redis
supervisorctl restart redis
```



### 监控系统 Zabbix

​	https://www.zabbix.com/download

​	安装完成之后提示403错误



-------------------------------------------未完待续---------------------------------------------------