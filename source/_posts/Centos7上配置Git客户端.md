---
title: Centos7上配置Git客户端
date: 2017-02-24 11:22:30
tags:
---

## 更新历史

日期 | 版本 | 备注
------------ | ------------- | ------------
27 Sep, 2016 | v1.0  | First version
27 Feb, 2017 | v1.1  | 再次整理

## 说明

本文档主要讲解如果在一台新购置的阿里云服务器（已经预装Centos7）上进行如下操作的过程：

* 服务器信息基本配置
* 安装Git

为了便于说明，步骤中敏感信息已经隐去，而采用如下Mock的信息代替：

云服务器：

- 外网地址：`120.11.111.111`
- 原主机名：`iZ11a1g1hh1Z`
- 修改后的主机名：`web1`
- root用户密码：`Welcome1$`
- 使用Git的用户名：`userone`
- userone的密码：`Test1$`
- Git服务器地址：123.22.222.222

终端：

- 外网地址：`106.10.100.100`

## 系统环境

- 服务器：阿里云ECS
- 配置：2核4G，1M带宽，40G高效云盘
- 系统：Centos 7.2

```
$ lsb_release -a
LSB Version:   	:core-4.1-amd64:core-4.1-noarch
Distributor ID:	CentOS
Description:   	CentOS Linux release 7.2.1511 (Core)
Release:       	7.2.1511
Codename:      	Core
```
- 主机名：test

## 配置步骤

### 修改主机名

新购置的服务器，默认只有一个`root`用户，且主机名比较复杂。为了便于管理，首先考虑修改主机名为容易识别的名称。本例为`web1`。

* 连接服务器

在终端命令行，使用`root`用户名和服务器的IP地址，通过ssh命令进行连接

`$ ssh root@120.11.111.111`  

```
The authenticity of host '120.11.111.111 (120.11.111.111)' can't be established.
ECDSA key fingerprint is SHA256:1jtoYFTDsTtuA3eyGhBILX2Bd2tzel8MJ0GD9ooHbXE.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '120.11.111.111' (ECDSA) to the list of known hosts.
root@120.11.111.111's password:
```

根据提示依次输入`yes`和密码`Welcome1$`后，登入系统，显示欢迎语

```
Welcome to aliyun Elastic Compute Service!

-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory

[root@iZ11a1g1hh1Z ~]#

```

从命令提示符信息，即`[root@iZ11a1g1hh1Z ~]#`，可以看到，用户名为`root`, 当前的主机名为阿里云分配的名称`iZ11a1g1hh1Z`。


* 永久修改主机名（重启后永久生效。配合`临时修改主机名`步骤，则不需要重启）

查询当前主机名

`$ hostname`  

```
iZ11a1g1hh1Z
```

打开配置文件

`$ vi /etc/sysconfig/network`

```
# Created by anaconda
PEERNTP=no
NETWORKING_IPV6=no
GATEWAY=120.11.111.111
HOSTNAME=iZ11a1g1hh1Z
```

将`HOSTNAME`配置项的值修改为`web1`，即

```
HOSTNAME=web1
```

保存退出。

* 临时修改主机名（立即生效，重启后失效。配合`永久修改主机名`步骤，则重启后也不失效）

`$ hostname web1`

查询当前主机名，若显示为`web1`，则已经生效

`$ hostname`

```
web1
```

* 重新连接服务器

`exit`退出当前的服务器连接，重新通过ssh连接，根据提示输入密码`Welcome1$`后登入服务器

`$ ssh root@120.11.111.111`  

```
root@120.11.111.111's password:
Last login: Tue Sep 20 22:58:21 2016 from 106.10.100.100

Welcome to aliyun Elastic Compute Service!

-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[root@web1 ~]#
```

可以看到命令提示符信息已经变为`[root@web1 ~]#`，

### 创建用户userone，并做Git仓库授权

使用`root`登录到云服务器

* 创建新用户：`userone`

`$ useradd userone`  

* 为用户`userone`创建密码

`[root@web1 ~]# passwd userone`

```
Changing password for user anni.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

根据提示信息，分别输入两次密码`Test1$`

* 将用户`userone`添加到sudoers列表（添加后，`userone`可以执行`su`命令）

`$ visudo`  

将打开配置文件；找到`root ALL`的内容，在下面添加`userone`用户的授权，结果如下

```
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
userone    ALL=(ALL)       ALL
```

* 生成SSH公钥

切换到`userone`帐号：

`$ su userone`

执行下面的命令(邮箱地址替换成为该服务器分配的邮箱地址)，一路回车：

`$ ssh-keygen -t rsa -C "web1@example.com" `

查看生成的秘钥：

`$ ls ~/.ssh/`

```	
id_rsa  id_rsa.pub
```

* Git授权

将`id_rsa.pub`提供给`Git服务器`，以便授权`Git服务器`代码的访问。

* 配置访问`git`服务器的主机信息

`$ vi ~/.ssh/config`

输入以下内容：

```
HOST git
HOSTNAME 123.22.222.222
USER git
```

修改`config`文件的权限

`$ chmod 600 ~/.ssh/config`

### 本地配置无密码登陆web1服务器


* 本地电脑配置`web1`服务器信息

`$ vi ~/.ssh/config`

输入以下内容：

```
HOST web1
HOSTNAME 120.11.111.111
USER userone
```

* 本地电脑修改`config`文件的权限

`$ chmod 600 ~/.ssh/config`


* 使用帐号`userone`登陆`web1`服务器：

```
$ ssh web1

anni@120.11.111.111's password:
```

* 设置无密码登陆

`$ vi ~/.ssh/authorized_keys`

将本地电脑的公钥内容，拷贝到`authorized_keys`中

* 修改`authorized_keys`权限

`$ chmod 600 ~/.ssh/authorized_keys`

* 重新登陆`web1`服务器，将不需要密码

`$ ssh web1`

### 安装Git

* Yum安装Git

```
$ sudo yum install git

[sudo] password for anni:
......
Is this ok [y/d/N]: y
......
```

### 测试Git是否能下载代码

* clone已经授权的代码库，例如`testing`

`$ git clone ssh://git/testing`

```
Cloning into 'testing'...
......

Receiving objects: 100% (216/216), 41.87 KiB | 0 bytes/s, done.
Resolving deltas: 100% (81/81), done.
```

