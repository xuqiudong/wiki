---
title: 倒台域名解析ddns
description: 我的linux设置ddns
published: true
date: 2022-08-01T01:42:25.846Z
tags: mine-linux
editor: markdown
dateCreated: 2022-08-01T01:42:25.846Z
---


> 根源还是因为电信的公网ip可能会不定期的改变，（和电信申请公网ip后，一会告诉我除非重启路由，不然ip不会变动。一会告诉我后台会有定时任务修改ip）。 不过 据我观察，首先重启路由肯定会改变ip，其次好像确实依然会不定期修改id，但是频率不是特别高。


### 原本的ddns  tplnk ddns

  原本使用的路由器是tplink的，这个路由器自带ddns，直接在路由器设置里新增即可。 
  开始的域名是xuqiudong.tpddns.cn.
  但是2022-07-31升级了电信套餐，宽带提升到1000M，电信师傅测试了下路由器，最高只能到100M。所以就给换了个电信送的中兴路由器。
  
  
### 新的ddns  freedns

中兴的路由器支持多种ddns。

我首先申请了个花生壳，但是送的免费二级域名没有可读性。

 
  