---
title: 反向代理域名缓存引起的504 Gateway Time-out
description: 反向代理域名缓存引起的504 Gateway Time-out
published: true
date: 2022-08-15T02:16:25.390Z
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
 1. 万能重启法。
   - 果然 `./nginx -s reload` 一下，立刻就ok了。
   
 但是重启nginx不是我要的解决方案啊，毕竟电信的ip要动，什么时候动，我不得而知啊，
 
##### nginx的 resolver  指令
> http://nginx.org/en/docs/http/ngx_http_core_module.html#resolver
https://nginx.org/en/docs/http/ngx_http_upstream_module.html
 
 resolver 指令有部分是商用的。
 
``` 
resolver 114.114.114.114 8.8.8.8 valid=1800s;
resolver_timeout 3s;
``` 
- `resolver` 可以在http全局设定，也可在server里面设定，我的设置在server里面，因为此处只需要处理这个子域名
- `resolver` 后面指定DNS服务器，可以指定多个，空格隔开
- `valid`设置DNS缓存失效时间。  也应适当考虑下效率什么的。
- `resolver_timeout` 指定解析域名时，DNS服务器的超时时间

