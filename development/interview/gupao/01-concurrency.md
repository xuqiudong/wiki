---
title: java并发变成基础
description: 
published: true
date: 2023-06-20T08:09:20.098Z
tags: 咕泡, 面试, 并发
editor: markdown
dateCreated: 2023-06-20T08:09:20.098Z
---

# 01 谈谈你对AQS的理解
1. AQS 是多线程同步器，它是 J.U.C 包中多个组件的底层实现，如 Lock、
CountDownLatch、Semaphore 等都用到了 AQS.
2. 从本质上来说，AQS 提供了两种锁机制，分别是排它锁，和 共享锁。
 - 排它锁，就是存在多线程竞争同一共享资源时，同一时刻只允许一个线程访问该共享资源，也就是多个线程中只能有一个线程获得锁资源，比如 Lock 中的ReentrantLock 重入锁实现就是用到了 AQS 中的排它锁功能。
 - 共享锁也称为读锁，就是在同一时刻允许多个线程同时获得锁资源，比如
CountDownLatch 和 Semaphore 都是用到了 AQS 中的共享锁功能。

# 02 lock 和 synchronized 区别
1. 从功能角度来看，Lock 和 Synchronized 都是 Java 中用来解决线程安全问题的工具。