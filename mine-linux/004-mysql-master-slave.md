---
title: 004-mysql8.0 主从备份(ubutu22.04到centos7.9)
description: mysql8.0 主从备份(ubutu22.04到centos7.9)
published: true
date: 2024-03-11T02:33:52.496Z
tags: mine-linux, mysql
editor: markdown
dateCreated: 2024-01-18T01:57:08.842Z
---

### 缘由

   我的主机：

- 阿里云主机centos7.9， 固定ip， xuqiduong.cn域名解析到此主机

- 家用小主机ubutu22.04， ip会动态改变， 通过ddns解析到xuqiudong.us.to

> - 项目和数据库等都放在家用主机上面。阿里云通过ngixn反向代理到家用主机。
>
> - 由于家用主机ip会变化，ddns会有一定的延迟，加上nginx反向代理域名会缓存ip(虽然在ngixn上我通过resolver解决缓存（参见我上一篇博文[临窗旋墨-反向代理域名缓存引起的504 Gateway Time-out (xuqiudong.cn)](https://xuqiudong.cn/detail/36)），但是考虑到效率，我依然设置了一定时间的缓存)。
> - 有鉴于此，当家用主机ip变化的时候，可能存在一定的时间无法访问xuqiudong.cn的情况。故在阿里云主机上同步部署了xuqiudong.cn项目并安装了数据库。
> - nginx配置的负载策略(backup)，当家用主机宕机的时候，访问阿里云本机项目。
> - 所以需要从家用主机ubuntu上同步数据库到阿里centos上。



### 数据库版本

##### **阿里云centos7.9  安装的是 8.0.30；**

由于CentOS 7默认安装的数据库是Mariadb,所以使用YUM命令是无法安装MySQL的，只会更新Mariadb。下面这篇博文有完整的安装过程，

  [centos7系统安装mysql8.0完整步骤 - ](https://cloud.tencent.com/developer/article/1849475)

##### **家用主机ubuntu22.04安装的是8.0.30-0ubuntu0.22.04.1**

通过apt安装，非常快。

```shell
apt-search mysql-server
sudo apt install mysql-server-8.0

# 看看默认账号和密码
sudo cat /etc/mysql/debian.cnf
# 登录mysql创建root 和授权
create user 'root'@'%' identified by 'password';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'xxxx';
```

取消本地地址绑定：

`/etc/mysql/mysql.conf.d/mysqld.cnf中注释掉 bind-address           = 127.0.0.1`

mysql服务管理：

```
sudo service mysql status # 查看服务状态
sudo service mysql start # 启动服务
sudo service mysql stop # 停止服务
sudo service mysql restart # 重启服务
```

卸载mysql

```
sudo apt purge mysql-*
sudo rm -rf /etc/mysql/ /var/lib/mysql
sudo apt autoremove
sudo apt autoclean
```



### 主从备份

#### 1 ubuntu主库配置

`/etc/mysql/mysql.conf.d/mysqld.cnf`

主要配置这几行就可以了, 配置完之后重启mysql， 重启玩看一下相关配置  `SHOW GLOBAL VARIABLES   LIKE '%...%'`  是否生效

```
#[必须]主服务器唯一ID
server-id=1
#[必须]启用二进制日志,无后缀的文件名。也可以是本地的路径/log/bin-log
log-bin=bin-log
#[可选]设置需要复制的数据库名称,默认全部记录。
binlog-do-db=qiudong
```

copy from file:

```
#
# The MySQL database server configuration file.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
# port          = 3306
# datadir       = /var/lib/mysql


# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmpdir
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
# 20220613  注释掉绑定本地地址
#bind-address           = 127.0.0.1
mysqlx-bind-address     = 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size         = 16M
# max_allowed_packet    = 64M
# thread_stack          = 256K

# thread_cache_size       = -1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP

# max_connections        = 151

# table_open_cache       = 4000

#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
#
# Log all queries
# Be aware that this log type is a performance killer.
# general_log_file        = /var/log/mysql/query.log
# general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
# slow_query_log                = 1
# slow_query_log_file   = /var/log/mysql/mysql-slow.log
# long_query_time = 2
# log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
# server-id             = 1
# log_bin                       = /var/log/mysql/mysql-bin.log
# binlog_expire_logs_seconds    = 2592000
max_binlog_size   = 100M
# binlog_do_db          = include_database_name
# binlog_ignore_db      = include_database_name
#
#start master config 20220902
#[必须]主服务器唯一ID
server-id=1
# [可选]设置需要复制的数据库名称,默认全部记录。
binlog-do-db=qiudong
# [可选] 0(默认)表示读写(主机),1表示只读(从机)
read-only=0
# 设置日志文件保留的时长,单位是秒 2592000 = 30天
#
binlog_expire_logs_seconds=2592000
# [可选]设置binlog格式(STATEMENT是基于sql语句的复制，ROW是基于行的复制，MIXED是混合模式
binlog_format=MIXED

```

#### 2 centos从库配置

`/etc/my.conf`

```
#[必须]从服务器唯一ID
server-id=2
#[可选]启用中继日志
relay-log=mysql-relay
#[可选] 0(默认)表示读写(主机),1表示只读(从机)
read-only=1
```

配置完成重启。

#### 3 从库连接主库

##### 3.1 查看主库日志  `show master status`;，

- 记录下file和position

##### 3.2 从库连接主库,执行如下命令

- https://dev.mysql.com/doc/refman/8.0/en/CHANGE-REPLICATION-source-to.html；

```
CHANGE MASTER TO
MASTER_HOST='主机的IP地址',
MASTER_USER='主机用户名',
MASTER_PASSWORD='主机用户名的密码',
MASTER_LOG_FILE='bin-log.具体数字',
MASTER_LOG_POS=Position的具体值;

-- 不过CHANGE MASTER 已经过期了，可以改为CHANGE REPLICATION SOURCE：
CHANGE REPLICATION SOURCE TO
SOURCE_HOST ='',
SOURCE_USER ='',
SOURCE_PASSWORD ='',
SOURCE_LOG_FILE ='binlog.000091',
SOURCE_LOG_POS =157;
```

**一些简单命令：**

```sql
# 查看slave状态
SHOW  REPLICA STATUS ;
#重置 删除SLAVE数据库的relaylog日志文件,并重新启用新的relaylog文件
RESET  REPLICA ALL;
# 停止
STOP REPLICA ;
# 启动
START REPLICA ;
```



### 测试：

修改主库数据，从库可以实时同步。





2022-09-02



参考：[ubuntu22 mysql8.0如何搭建主从复制？](https://blog.csdn.net/wuguandi/article/details/126572693)



 