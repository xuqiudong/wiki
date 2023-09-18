---
title: 05-Zookeeper常见问题
description: Zookeeper常见问题
published: true
date: 2023-09-18T07:03:47.349Z
tags: 咕泡, 面试, zookeeper
editor: markdown
dateCreated: 2023-09-18T07:03:47.349Z
---

# 05-Zookeeper常见问题

## 01 分布式锁，Zookeeper 和 Redis 比较
1. Redis 可以通过两种方式来实现：
  - 1.1 利用 Redis 提供的`SET key value NX PX milliseconds`指令，这个指令是设置一个 key-value，如果 key 已经存在，则返回 0，否则返回 1，我们基于这个返回值来判断锁的占用情况从而实现分布式锁。
  - 1.2 基于 Redission 客户端来实现分布式锁，`lock（）`和`unlock()`方法
    - a. redisson 所有指令都通过 lua 脚本执行并支持 lua 脚本原子性执行
    - b. redisson 中有一个 watchdog ,它会在你获取锁之后，每隔 10 秒帮你把 key 的超时时间设为 30s，就算一
直持有锁也不会出现 key 过期了。“看门狗”的逻辑保证了没有死锁发生。