---
title: 动态域名解析ddns
description: 我的linux设置ddns
published: true
date: 2022-08-08T03:06:40.092Z
tags: mine-linux
editor: markdown
dateCreated: 2022-08-01T01:42:25.846Z
---

### 为什么需要动态域名解析

**电信是不开放80和443等常用端口的**
 
> 根源还是因为电信的公网ip可能会不定期的改变，（和电信申请公网ip后，一会告诉我除非重启路由，不然ip不会变动。一会告诉我后台会有定时任务修改ip）。 不过 据我观察，首先重启路由肯定会改变ip，其次好像确实依然会不定期修改id，但是频率不是特别高。


### 关于我的域名

#### 我的顶级域名
 * 首先是从阿里云申请的顶级域名 xuqiudong.cn, 二级绑定了一些简单的应用，然后通过ali-sdk ，手动编写了定时器，定时检测域名变化，然后重新解析。
 
 * 当前有购买阿里云的云服务器，所以在把相关服务迁徙到个人的物理linux机器上的同时，通过nginx反向代代理我的linux物理机的相关服务的。
 
 * 后期再购买云服务器的话，考虑买一个轻量级的。如果哪一天不再购买云服务器.鉴于电信不开发80和443端口，则把xuqiudong.cn做url转发，转发到二级带端口的域名。
 
#### 我的免费域名
* 上面说的定时器，我设置的频率不是很大，可能有一定的延时性。
* 所以还是想跟随路由器上面支持的ddns，再绑定一个二级免费域名。最主要是方面ssh连接。
* 所以在路由器做了端口转发后，还是想在路由器上绑定一个免费的二级域名一般做ssh访问。

### 原本的ddns  tplnk ddns

  原本使用的路由器是tplink的，这个路由器自带ddns，直接在路由器设置里新增即可。 
  开始的域名是xuqiudong.tpddns.cn.
  但是2022-07-31升级了电信套餐，宽带提升到1000M，电信师傅测试了下路由器，最高只能到100M。所以就给换了个电信送的中兴路由器。
  
  
### 新的ddns  freedns

中兴的路由器支持多种ddns。
1.  [花生壳](https://hsk.oray.com/) : 送的免费二级域名 http://2468011t2l.goho.co/ 没有可读性。
2.  [freedns](https://freedns.afraid.org/): 申请了 xuqiudong.us.to
   - 官网提供了API接口实现ddns
   - 写了个定时器，每小时定时访问接口，以实现DDNS
   - `crontab` : `0  */1 * * * /xqd/mine-shell/freedns/freedns.sh`
   - `freedns.sh` :  
   ```
   
#!/bin/bash

log=/xqd/mine-shell/freedns/out.log
now=`date '+%Y-%m-%d %H:%M:%S'`
echo $now >> $log
curl -k https://freedns.afraid.org/dynamic/update.php?czhLWkJFRkFIa1dJTjFLRjF1Z3I3amNnOjIwNjAzMDU5 >> $log

   ```
   - [Setup DDNS with FreeDNS](https://www.filegott.se/2017/04/15/setup-ddns-with-freedns/)
   
 
   
3.  dyndns: 申请账号页面提交了没反应，F12看是报错了。




 
  