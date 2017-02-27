---
title: Centos7上安装并配置Mysql(Mariadb)
date: 2017-02-27 15:37:20
tags:
---

## 更新历史

日期 | 版本 | 备注
------------ | ------------- | ------------
27 Feb, 2017 | v1.0  | Initial Version

## 说明

本文档主要讲解如何在阿里云服务器上安装mariadb(mysql)并完成基本配置的步骤，具体包括

* 安装mysql(mariadb)
* 配置mysql(mariadb)

为了便于说明，步骤中敏感信息已经隐去，而采用如下Mock的信息代替：

云服务器：

- 外网地址：`120.11.111.111`
- 主机名：`web1`
- mysql的root用户密码：`Welcome1$`
- mysql的新建用户：`userone`
- mysql的userone用户密码：`123456`

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
- 主机名：web1

## 配置步骤

- Yum安装Mariadb

`$ sudo yum -y install mariadb*`

- 查看Mariadb版本

`$ mysql --version`

```
mysql  Ver 15.1 Distrib 5.5.52-MariaDB, for Linux (x86_64) using readline 5.1
```
- 启动Mariadb服务并尝试连接

`$ sudo systemctl start mariadb`
`$ mysql`

```
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 5 
Server version: 5.5.50-MariaDB MariaDB Server

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

- 安全配置 MySQL

使用mariadb自带的安全设置工具，以设置 root 密码、删除匿名用户、禁止 root 远程登录、删除 test 数据库、重新加载权限表。
输入命令后基本上一路Y下去。下面的命令执行结果，仅仅列出了需要输入的语句：

`$ mysql_secure_installation`
```
Enter current password for root (enter for none):

Set root password? [Y/n] y
New password: Welcome1$

Remove anonymous users? [Y/n] y

Disallow root login remotely? [Y/n] y

Remove test database and access to it? [Y/n] y

Reload privilege tables now? [Y/n] y
```

- 创建新用户并设置密码

`$ mysql -uroot -p`

`mariadb> create user 'userone'@'%' identified by '123456'`

这样就创建了一个新的数据库帐户'userone'，其密码为'123456'。

这样就可以通过用户userone访问数据库了：

`$ mysql -uuserone -p123456`

可以用下面的命令修改该密码：

`mysql> set password = password('654321') `

- 设置Mysql编码

打开mysql.cnf：

`$ vi /etc/mysql.cnf`

在文件的[mysqld]段，加上编码配置；并添加[client]段，同样配置编码：

```
[mysqld]
character-set-server = utf8
collation-server = utf8_general_ci

[client]
default-character-set=utf8
```

这里的字符编码utf8必须存在于`/usr/share/mysql/charsets/Index.xml`中，且拼写一致，可以这样验证：

`$ cat /usr/share/mysql/charsets/Index.xml |grep "utf8"`
```
<charset name="utf8">
<collation name="utf8_general_ci"     id="33">
<collation name="utf8_bin"        id="83">
```

配置完成后，重启使其生效(若重启失败，考虑是否mysql.cnf配置有误)：

`$ sudo systemctl restart mariadb`

`$ mysql -uuserone -p123456`

`mariadb> show variables like '%char%';`

所有编码的变量，都已经变成utf8：

```
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```
- 允许userone远程访问数据库

谨慎决定，是否允许远程访问数据库。若风险可控，可以通过如下方式开始远程访问。

使用超级用户root登录数据库：

`$ mysql -uroot -p`

授权用户userone可以访问mysql中的任意数据库和任意表；%表示所有IP，ALL表示所有权限；

`mariadb> GRANT ALL PRIVILEGES ON *.* TO 'userone'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;`

让授权生效：

`mariadb> FLUSH PRIVILEGES;`

这时候，可以尝试在另一台机器上访问该数据库；由于默认数据库连接的端口3306并没有打开，因此将报连接错误

`$ mysql -h 120.11.111.111 -uuserone -p123456`
```
Warning: Using a password on the command line interface can be insecure.
ERROR 2003 (HY000): Can't connect to MySQL server on '120.11.111.111' (61)
```
- 打开远程访问端口3306

`$ firewall-cmd --zone=public --add-port=3306/tcp --permanent`

```
success
```

`$sudo firewall-cmd --reload`

```
success
```

这时候，可以成功地从另一台机器上访问该数据库：

`$ mysql -h 120.11.111.111 -uuserone -p123456`

至此，Mysql配置完毕。
