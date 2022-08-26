---
title: 软考案例分析随手记
description: 系统机构是案例分析随手记
published: true
date: 2022-08-26T08:11:12.863Z
tags: study, 软考
editor: markdown
dateCreated: 2022-08-26T07:57:31.531Z
---

> 系统架构设计师
### 001. 常见反规范化技术：
1. 增加冗余列
2. 增加派生列
3. 重新组表：
4. 垂直分割表：
5. 水平分割表：

### 002. 范规范化设计数据一致性
1. 定时器批处理
2. 应用程序中进行逻辑处理
3. 触发器的方式

### 003 redis和mysql数据同步方案
1. 一致性高，实时同步： 查询先查缓存；更新时候，先入库，再删缓存
2. 并发高， 异步队列同步，如kafaka,rabbitmq
3. 采用同步工具，如阿里的canal，原理类似mysql slave和master，监控binlog的方式来更新缓存
4. 自定义UDF函数的方式，地mysql的API进行编程，利用触发器同步缓存