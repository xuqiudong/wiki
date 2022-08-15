---
title: 反向代理域名缓存引起的504 Gateway Time-out
description: 反向代理域名缓存引起的504 Gateway Time-out
published: true
date: 2022-08-15T01:47:23.978Z
tags: mine-linux, nginx
editor: markdown
dateCreated: 2022-08-15T01:47:23.978Z
---


>    早上来访问 wiki.xuqiudong.cn 居然504 Gateway Time-out
 这个域名是阿里云的二级域名，通过nginx反向代理到自己的家庭主机：xuqiudong.us.to:3000
 好像是昨天修电灯拉了电闸，电脑忘了重启。
 重启后依然不能访问。 这不对劲啊，虽然每次重启ipH会修改，不但我在路由器里设置了freddDNS的ddns，也写了定时脚本重新解析域名的啊。
 > 
 > 直接访问xuqiudong.us.to:3000， 是ok的，看来是nginx反向代理的问题。打开error日志查看，显示upstream的还是以前的ip地址。 懂了，dns被ngixn缓存了。
 
 
 #### 解决办法：
 - 万能重启法。
