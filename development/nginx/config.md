---
title: nginx配置中的一些问题
description: 一些nginx在配置中遇到的问题
published: true
date: 2022-07-29T01:15:49.562Z
tags: development, nginx
editor: markdown
dateCreated: 2022-07-29T01:15:46.624Z
---

###  nginx +  springboot 重定向端口丢失以及https变为http：
[nginx中proxy_set_header Host $host的作用及$proxy_host，$host与$http_host的区别](https://www.cnblogs.com/goloving/p/13663843.html)

```
 # 把发送给客户端的http 转为https
 proxy_redirect http:// https://;
 proxy_set_header   Host             $http_host;
 proxy_set_header   X-Real-IP        $remote_addr;
 proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
 proxy_set_header X-Forwarded-Proto $scheme;
 proxy_set_header X-Forwarded-Port $server_port;

```