---
title: 我的linux
description: 家庭版本的迷你主机充当个人的linux服务器
published: true
date: 2024-01-19T09:13:54.516Z
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

## 服务地址

### ~~基于tplink提供的免费ddns地址：xuqiudong.tpddns.cn~~

1. 空nginx：  http://xuqiudong.tpddns.cn:81/
2. portainer: http://xuqiudong.tpddns.cn:82/  
  - nginx代理了是9000上的端口
  - portainer 运行在docker上
3. wiki.js：   http://xuqiudong.tpddns.cn:3000/  
4. nextcloud 个人云盘，同步盘 http://xuqiudong.tpddns.cn:83/  
 - 基于php 运行在7000端口，被nginx反向代理 

## 更换了路由器不再是tplink，因此xuqiudong.tpddns.cn失效
> 2022-08-01
### 申请了freedns的免费二级域名xuqiudong.us.to
当前路由器支持对[freedns](xuqiudong.tpddns.cn)的ddns，另外也写了定时器定时更新ip解析
1. 空nginx：  http://xuqiudong.us.to:81/
2. portainer: http://xuqiudong.us.to:82/  
  - nginx代理了是9000上的端口
  - portainer 运行在docker上
3. wiki.js：   http://xuqiudong.us.to:3000/  
4. nextcloud 个人云盘，同步盘 http://xuqiudong.us.to:83/   or https://home.xuqiudong.cn:88/ 
 - 基于php 运行在7000端口，被nginx反向代理 

## xuqiudong.cn域名和子域名的安排
> 主要是因为电信不开放 80和443端口。 如果后期不购买云服务器的话，就要带端口访问了。 暂时通过云服务器上nginx反向代理到" xuqiudong.us.to :port。"


1. 博客首页：xuqiudong.cn 重定向到 https://blog.xuqiudong.cn:88
2. wiki首页: https://wiki.xuqiudong.cn:88
3. 导航： https://nav.xuqiudong.cn:88
4. 微信： https://wechat.xuqiudong.cn:88
5. 附件：https://attachment.xuqiudong.cn:88
6. 后台：https://console.xuqiudong.cn:88
7. 在线日志查看：https://readlog.xuqiudong.cn:88

