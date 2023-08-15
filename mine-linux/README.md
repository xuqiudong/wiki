---
title: 我的linux
description: 家庭版本的迷你主机充当个人的linux服务器
published: true
date: 2023-08-15T02:43:04.031Z
tags: mine-linux
editor: markdown
dateCreated: 2022-06-13T06:00:53.930Z
---

> 我的家用linux服务器

## 目录

[001- 买个迷你主机当`linux`服务器使用](/mine-linux/001)

[002-新建systemctl  service的java开机启动项](/mine-linux/002)

[003-动态域名解析ddns](/mine-linux/003-ddns)

[004-mysql8.0主从备份](/mine-linux/004)

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
