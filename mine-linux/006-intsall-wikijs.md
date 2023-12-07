---
title: 006-安装wikijs
description: wiki.js安装过程
published: true
date: 2023-12-07T02:36:19.014Z
tags: mine-linux, software
editor: markdown
dateCreated: 2023-12-07T02:36:19.014Z
---

### 安装wiki.js
> 好像是22年5月的时候做的

   一直主要是本地markdown（typora） + 坚果云的。

 偶尔把一些自己写的东西放在自己的博客、以及gitee page（通过docsify把Markdown转为静态页面）上。



​    这一次不再使用云服务器了。想着还是要安装一个只是管理。

####     我的三种知识管理选择：

#####    第一种   docsify + gitee Page

> 之所以没有使用github page 还是因为网络不稳定。  但是，gitee有点抽风，莫名其妙的正常内容有的时候审核不通过。  而且push到gitee之后还需要手动发布。

#####  第二种   gitbook

> 主要还是网络问题

##### 第三种wiki.js

> 看了下页面，还可以，依赖于node.js。
>
> 既然有自己的服务器，可以试着安装一下，然后和github同步。



#### 官方安装手册：

[Installation | Wiki.js (requarks.io)](https://docs.requarks.io/en/install)

  

docker 还没安装，就先手动安装一下wiki吧

[Linux | Wiki.js (requarks.io)](https://docs.requarks.io/install/linux)

```
CREATE  DATABASE wiki DEFAULT CHARSET utf8mb4;

-- mysql8 
CREATE USER 'wiki'@'localhost' IDENTIFIED BY '12345678';
GRANT ALL PRIVILEGES ON wiki.* TO 'wiki'@'localhost' WITH GRANT OPTION;

FLUSH
    PRIVILEGES;
```

1. 安装node.js

   > sudo apt install nodejs
   >
   > node -v  显示版本为 v12.22.9

按照官网一步步来即可



#### wiki.js和git同步

> [wiki.js配置 - 简书 (jianshu.com)](https://www.jianshu.com/p/0b5e3b4e6ab1)

1 . 生成秘钥(勿输入密码)

> ```undefined
> ssh-keygen -t rsa -b 4096
> ```

2. 将秘钥配置到github
3. 在wiki管理页面的存储下的git，按部就班配置即可



在保存页面的时候遇到的一个错误：

Error when running job render-page: EACCES: permission denied, open '/qiudong/soft/wiki-service/wiki/data/cache/6dc624cafd0dab4623a2f44cd0c91054ee7b8131.bin'

我修改了启动脚本中的用户，把nobody改为当前用户后重启。

