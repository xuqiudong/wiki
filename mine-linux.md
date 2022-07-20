---
title: 我的linux
description: 家庭版本的迷你主机充当个人的linux服务器
published: true
date: 2022-07-20T01:10:12.736Z
tags: mine-linux
editor: markdown
dateCreated: 2022-06-13T06:00:53.930Z
---

> 我的家用linux服务器

## 目录

[001- 买个迷你主机当`linux`服务器使用](/mine-linux/001)

## 服务地址

### 基于tplink提供的免费ddns地址：xuqiudong.tpddns.cn

1. 空nginx：  http://xuqiudong.tpddns.cn:81/
2. portainer: http://xuqiudong.tpddns.cn:82/  
  - nginx代理了是9000上的端口
  - portainer 运行在docker上
3. wiki.js：   http://xuqiudong.tpddns.cn:3000/  
4. nextcloud 个人云盘，同步盘 http://xuqiudong.tpddns.cn:83/  
 - 基于php 运行在7000端口，被nginx反向代理 
 

## xuqiudong.cn域名和子域名的安排
> 主要是因为电信不开放 80和443端口。
1. 博客首页：xuqiudong.cn 重定向到 https://blog.xuqiudong.cn:88
2. wiki首页: https://wiki.xuqiudong.cn:88
3. 导航： https://nav.xuqiudong.cn:88
4. 微信： https://wechat.xuqiudong.cn:88
5. 附件：https://attachment.xuqiudong.cn:88
6. 后台：https://console.xuqiudong.cn:88
7. 在线日志查看：https://readlog.xuqiudong.cn:88