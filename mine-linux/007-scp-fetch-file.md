---
title: 007-scp从云主机拉取ssl证书到物理linux主机
description: 通过scp从云主机拉取ssl证书到物理linux主机
published: true
date: 2024-02-29T07:42:53.633Z
tags: 
editor: markdown
dateCreated: 2024-01-18T01:57:22.425Z
---

# scp从云主机拉取ssl证书到物理linux主机

### 背景

  主域名是解析到云主机上的，然后反向代理到我的物理linux小主机。但这样是会消耗流量的，某些资源，比如图片希望放在linux主机上，省却流量消耗。
    
  比如当前wiki.xuqiudong.cn解析的地址为云主机，然后代理到物理linux小主机。
    
  wiki上的文章我一般是使用typora本地编辑的。文章中插入的图片我是通过Picgo插件上传到我的linux主机上部署的nexctloud的，默认使用的是ddns域名，没有配置ssl。
    
  这个时候就产生了一个问题，wiki是https，文章里面的图片链接为http，这种情况下浏览器是禁止的。
  所以，我重新解析了一个域名到linux主机，映射到nextcloud，并把它配置为https，这个时候就需要SSL证书了。
    
  我的云主机上的SSL证书是用过[letsencrypt](https://letsencrypt.org/zh-cn/)免费申请的，通过acme自动化做一些校验和自动延长的的处理。
    
  而我的linux主机用于家用，是默认不开通80和443端口的，处理一些验证和自动化脚本有点费劲。所以就考虑在云主机上通配符泛域名ssl证书，然后linux主机上定时拉取过来。
   
   
### 公钥认证

**目的**：允许物理机使用scp命令拉取云主机上的ssl证书。

1. 物理机生成公钥：
```shell
ssh-keygen -t rsa -b 4096
```
在家目录/.ssh/下生成公钥id_rsa.pub和私钥id_rsa
2. 把物理机的公钥追加到云主机家目录.ssh/下的authorized_keys中
```shell
cat id_rsa.pub >> authorized_keys
```
### 使用scp命令拉取文件到本地
`scp [可选参数] file_source file_target `

1. 编写拉取ssl证书和重启nginx脚本`fetch_ssl_pem.sh`
```shell
#!/bin/bash
scp -r root@117.72.8.64:/etc/nginx/pem/* /etc/nginx/pem/
echo 'fetch ssl pem file from jdcloud!'
systemctl restart nginx
echo 'restart nginx'
```
2. 赋予执行权限，并追加到定时器，每周执行一次
```shell
chmod +x fetch_ssl_pem.sh
```
nginx当时是root权限的：
`sudo crontab -e`
```shell
#01 每周日0点30分执行脚本
30 0 * * 0  /xqd/mine-shell/ssl/fetch_ssl_pem.sh
```

### 遇到的一个小问题

> Permissions 0664 for '/root/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/root/.ssh/id_rsa": bad permissions

解决方案：取消所属组和其他人的read权限取消即可

`sudo chmod 600 /root/.ssh/id_rsa`

----

> 关于SSL证书以前就记录过，忘了放到我的wiki上了，这里在备注一下

###   关于https

阿里云免费提供20个ssl证书，每个有效期一年，且不支持通配符域名。

但是我感觉用[Let's Encrypt](https://letsencrypt.org/)的人还是很多的，考虑一下使用Let's Encrypt也不错。不过它的有效期只有90天，应该是考虑到安全以及鼓励自动化。

不过他的[验证方式](https://letsencrypt.org/zh-cn/docs/challenge-types/) 中最常见的HTTP-01验证还是需要80和443端口的（万恶的电信封端口啊）。好在DNS-01 验证方式只要在域名的TXTX记录中放置特定值来证明控制域名的 DNS 系统即可，不好的地方就是证书的续期有点麻烦。

查看了下[此处提供了此类 DNS 提供商的列表](https://community.letsencrypt.org/t/dns-providers-who-easily-integrate-with-lets-encrypt-dns-validation/86438)， 能找到阿里云，看来这个方法是行得通的。

| DNS Hosting Provider                                         | **ACME Client Support**                                      | **Cost**                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Aliyun (CN) 123](https://cn.aliyun.com/) & [Alibaba Cloud DNS (EN) 83](https://www.alibabacloud.com/product/dns) | [acme.sh 9.4k](https://github.com/Neilpang/acme.sh/tree/master/dnsapi), [lego 1.6k](https://go-acme.github.io/lego/dns/), [Posh-ACME 1.6k](https://github.com/rmbolger/Posh-ACME) | Bundled with domain registration or [Cloud DNS pricing 74](https://www.alibabacloud.com/product/dns/pricing) |

然后ACME (自动证书管理环境 - Automatic Certificate Management Environment) 客户端可以使用[acme.sh](https://github.com/acmesh-official/acme.sh)（还有正文版readme，贴心）

[acme.sh的使用](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E)：

###  acme的安装

> ```
> git clone https://github.com/acmesh-official/acme.sh.git
> cd acme.sh
> ./acme.sh --install -m my@example.com
> #  提示我独立主机 最好安装socat，那就安装吧
> sudo apt install socat
> ```



### 添加 通配符证书 （本域名申请自阿里云）

（`使用域名DNS服务商API方式只生成SSL证书不创建网站配置文件`）

1. 可以把对应的token 添加到环境变量中  vim ~/.bash_profile  或者export

```js
export Ali_Key="....."
export Ali_Secret="...."
# 生成通配符证书，dns_ali 为厂商缩写
./acme.sh --issue --dns dns_ali -d xuqiudong.cn -d *.xuqiudong.cn

acme.sh  --installcert  -d  xuqiudong.cn   \
        --key-file   /etc/nginx/pem/key.pem  \
        --fullchain-file /etc/nginx/pem/wildcard.pem \
        --reloadcmd  "service nginx force-reload"
```

./acme.sh  --installcert  -d  xuqiudong.cn   \
        --key-file   /usr/local/nginx/pem/key.pem  \
        --fullchain-file /usr/local/nginx/pem/full.pem \
        --reloadcmd  "service nginx force-reload"



./acme.sh  --installcert  -d  xuqiudong.cn   \
        --key-file   /etc/nginx/pem/key.pem  \
        --fullchain-file /etc/nginx/pem/full.pem \
        --reloadcmd  "service nginx force-reload"



-

```
-- win10下 检测 txt 域名解析是否成功
nslookup -type=txt _acme-challenge.xuqiudong.cn 223.5.5.5
```



```nginx
ssl on;
ssl_certificate /etc/nginx/pem/wildcard.pem;
ssl_certificate_key /etc/nginx/pem/key.pem;

```

 -- 更新证书

````
acme.sh --renew -d xuqiudong.cn \
  --yes-I-know-dns-manual-mode-enough-go-ahead-please
````


一些参考文章：

- [acme从letsencrypt 生成免费通配符/泛域名SSL证书并自动续期 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1736866)

- https://freexyz.cn/server/104425.html