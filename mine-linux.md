---
title: 我的linux
description: 家庭版本的迷你主机充当个人的linux服务器
published: true
date: 2024-03-25T01:30:28.724Z
tags: mine-linux
editor: markdown
dateCreated: 2024-01-18T01:57:32.119Z
---

> 我的家用linux服务器

## 目录

- [000-我的服务应用导航](/mine-linux/000-serve-application-navigation)
- [001- 买个迷你主机当`linux`服务器使用](/mine-linux/001)
- [002-新建systemctl  service的java开机启动项](/mine-linux/002)
- [003-动态域名解析ddns](/mine-linux/003-ddns)
- [004-mysql8.0主从备份](/mine-linux/004-mysql-master-slave)
- [005-购买京东云服务器](/mine-linux/05-购买京东云服务器)
- [006-安装wikijs](/mine-linux/006-intsall-wikijs)
- [007-scp从云主机拉取ssl证书到物理linux主机](/mine-linux/007-scp-fetch-file)
- [008-Linux磁盘分区挂载与LVM](/mine-linux/008-Disk_partition_mounting_and_LVM)
- [009-cloud-disk](/mine-linux/009-cloud-disk)
- [010-install-smaba](/mine-linux/010-install-smaba)

## 服务地址

### ~~基于tplink提供的免费ddns地址：xuqiudong.tpddns.cn~~

## 更换了路由器不再是tplink，因此xuqiudong.tpddns.cn失效
> 2022-08-01
### 申请了freedns的免费二级域名xuqiudong.us.to
当前路由器支持对[freedns](https://freedns.afraid.org/)的ddns，另外也写了定时器定时更新ip解析
1. 空nginx：  http://xuqiudong.us.to:81/
2. portainer: http://xuqiudong.us.to:9000/  
  - 准备使用nginx：82代理了是9000上的端口
  - portainer 运行在docker上
3. wiki.js：   http://xuqiudong.us.to:3000/ ← https://wiki.xuqiudong.cn/
4. filebrowser 个人云盘，同步盘 http://xuqiudong.us.to:40002/

## ~~xuqiudong.cn域名和子域名的安排~~
> 主要是因为电信不开放 80和443端口。 如果后期不购买云服务器的话，就要带端口访问了。 暂时通过云服务器上nginx反向代理到" xuqiudong.us.to :port。"


1. 博客首页：xuqiudong.cn 重定向到 https://blog.xuqiudong.cn:88
2. wiki首页: https://wiki.xuqiudong.cn:88
3. 导航： https://nav.xuqiudong.cn:88
4. 微信： https://wechat.xuqiudong.cn:88
5. 附件：https://attachment.xuqiudong.cn:88
6. 后台：https://console.xuqiudong.cn:88
7. 在线日志查看：https://readlog.xuqiudong.cn:88

----
购机选项：
- 炙影Firebat 12代N100-Intel酷睿n100迷你主机
- N5105
----
port foeward records
> 3306                       ALLOW       Anywhere
82                         ALLOW       Anywhere
3000                       ALLOW       Anywhere
10084                      ALLOW       Anywhere
10081                      ALLOW       Anywhere
10083                      ALLOW       Anywhere
9876                       ALLOW       Anywhere
81                         ALLOW       Anywhere
10911                      ALLOW       Anywhere
2375                       DENY        Anywhere
9000                       ALLOW       Anywhere
40001                      ALLOW       Anywhere
40002                      ALLOW       Anywhere
83                         ALLOW       Anywhere
883                        ALLOW       Anywhere
445           

- 22:
- 81:router
- 82: default nginx
- 9876/445:samba  LAN sharing
- 883/ 40002 filebrower    nginx/itself
- 83： none (filebrower)
- 3000: wiki
- 3306
- 9000 docker
- 10081: sso
- 10083: console
- 10084: blog
- 2375: docker  closed
> 22
80-88
10081-10181
3306
3000
5200-5300alibaba
9200
9876
10911
4xxxx docker
  


