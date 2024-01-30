---
title: 我的linux文件备份
description: 备份linux上的文件
published: true
date: 2024-01-30T09:37:56.521Z
tags: api, mine-linux
editor: markdown
dateCreated: 2024-01-30T09:25:43.957Z
---

# 我的linux备份
> 自从我的linux小主机重装系统，导致相关文件丢失后，就心有余悸。感觉还是老老实实的做好备份比较好。

## 需要备份的文件
1. 附件文件
	- 我的项目中的附件文件
2. 上传到应用的文件
 - 比如上传到nextcloud的文件
 
3. 数据库文件
 - 定时备份上传
4. 相关配置文件
 - 如nginx
5. 手写的一些文档
 - 如tips
 
## 上传到哪里
1. 坚果云： 没找到合适的API
2. 百度云： 需手写上传， 暂定 [百度云盘API](https://pan.baidu.com/union/doc/olkuuy5kz)

## 如何上传
1. 编写上传到百度的相关项目（记录上传日志和文件类型）
2. 定时备份相关文件到指定的目录，然后1提供调用上传接口

