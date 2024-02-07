---
title: 我的linux文件备份
description: 备份linux上的文件
published: true
date: 2024-02-07T07:14:22.710Z
tags: api, mine-linux
editor: markdown
dateCreated: 2024-01-30T09:25:43.957Z
---

# 我的linux备份
> 自从我的linux小主机重装系统，导致相关文件丢失后，就心有余悸。感觉还是老老实实的做好备份比较好。

## 实现方案考虑
1. 通过inotify-java监控需要备份的文件夹
2. 然后把变化的文件上传到百度云盘, 并且记录到日志表

> 对于已经存在的文件，或者在未监控时候产生的文件如何处理


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


## 编码
### 备份类别
- 数据库自动备份
> 1. 增量备份 脚本定时备份数据库数据，然后调用接口 上传到百度
> 2. 相对目录：/backup/mysql/date-database.sql ；date-datascheme.sql

### 文件夹备份
- 配置指定的文件夹，
> 1. 脚本定时打包为zip 然后调用接口上传到百度，然后删除zip
> 2. 相对目录 /backup/xxcategory/date-categoryName.zip

### 应用数据备份
- 备份某个应用的文件
> 1. 第一次备份全部
> 2. 后续备份 文件的更新时间为上一次备份之后的文件


### 备份配置表
- 类别   目录

### 备份记录表
- 类别
- 配置表id
- 文件名
- 路径



[通过接口上传文件到百度网盘](https://xie.infoq.cn/article/989ba52abf905fdabc99fe340)


