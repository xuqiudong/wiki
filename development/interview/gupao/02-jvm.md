---
title: 02-JVM常见问题
description: JVM相关的一些问题
published: true
date: 2023-08-18T07:42:00.699Z
tags: jvm, 咕泡, 面试
editor: markdown
dateCreated: 2023-08-17T07:22:28.250Z
---

# 02-JVM相关问题

## 01 CPU 飙高系统反应慢怎么排查
1. CPU最小执行单元是线程。
2. 导致 CPU 飙高的原因有几个方面：
 - 2.1 上下文切换过多  (文件 IO、网络 IO、锁等待、线程阻塞等操作)
 - 2.2 CPU 资源过度消耗:创建了大量的线程，或线程一直占用CPU 资源无法被释放，比如死循环！
3. 解决方案：通过 `top` 命令，找到 CPU 利用率较高的进程，在通过 `Shift+H` 找到进程中 CPU 消耗过高的线程
  - 如过是同一个线程，说明程序中存在线程长期占用 CPU 没有释放的情况，这种情况直接通过 jstack 获得线程的 Dump 日志，定位到线程日志后就可以找到问题的代码。
 - 如果线程 id 不断变化，说明线程创建过多，需要挑选几个线程id，通过 jstack 去线程 dump 日志中排查。
5. 有可能定位的结果是程序正常，只是在 CPU 飙高的那一刻，用户访问量较大，导致系统资源不够。 

## 02 线上OOM问题排查
1. 内存泄漏或内存溢出
2. 设置JVM参数，增大内存
3. OOM时候先获取dump文件(`-XX: +HeapDumpOnOutOfMemoryError` 或 `jmap -dump:[live],format=b,file=<file-path> <pid> `).然后使用 MAT 工具来分析 Dump 文件.
  - 3.1 内存泄漏，可进一步通过工具查看泄漏对象到 GC Roots 的引用链。掌握了泄漏对象的类信息和 GC Roots 引用链的信息，就可以比较准确地定位泄漏代码的位置。
  - 3.2 如果是普通的内存溢出，确实有很多占用内存的对象，那就只需要提升堆内存空间即可。

# 03 什么是双亲委派
1. 当需要加载一个 class 文件的时候，首先会把这个 class 的查询和加载委派给父加载器去执行，如果父加载器都无法加载，再尝试自己来加载这个 class。
2. Bootstrap ClassLoader，Extension ClassLoader，Application ClassLoader，自定义类加载器
3. 双亲委派的作用：
  - 3.1 安全性， 对于核心类库中的类，就没办法去破坏
  - 3.2 避免重复加载导致程序混乱的问题，因为如果父加载器已经加载过了，那么子类就没必要去加载了。
4. 重写loadClass方法就可以破坏 双亲委派 ;使用线程上下文加载器，可以通过 java.lang.Thread 类的setContextClassLoader()方法来设置当前类使用的类加载器类型

## 04 JVM 如何判断一个对象可以被回收
1. **引用计数为0的时候**。 需要额外的空间来存储引用计数器，但是它的实现很简单，而且效率也比较高。主流的 JVM 都没有采用这种方式，因为引用计数器在处理一些复杂的循环引用或者相互依赖的情况时，可能会出现一些不再使用但是又无法回收的内存，造成内存泄露的问题。
2. **可达性分析**。先确定一系列肯定不能回收的对象作为 GC root，比如虚拟机栈里面的引用对象、本地方法栈引用的对象等，然后以 GC ROOT 作为起始节点，向下搜索，去寻找它的直接和间接引用对象，对象不可到达，则回收。

## 05 G1垃圾回收
https://juejin.cn/post/7025212933428740110

## 06 JVM 分代年龄为什么是 15 次？可以 25 次吗
> 不可以，对象头中使用4bit存储GC年龄，最大为15.

1.  JVM 的 heap 内存里面，分为 Eden Space、Survivor Space(from  to)、Old Generation
2. new 对象会在 Eden Space 分配一块内存空间来存储这个对象。当 Eden Space 的内存空间不足的时候，会触发 Young GC 进行对象回收。那些因为存在引用关系而无法回收的对象，JVM 会把它们转移到 Survivor Space。
3. Survivor Space 内部又分为 From 区和 To 区，刚从 Eden 区转移过来的对象会分配到 From 区，每经历一次 Young GC，这些没有办法被回收的对象就会在 From 区和 To 区来回移动，每移动一次，这个对象的GC 年龄就加 1。默认情况下 GC 年龄达到 15 的时候，JVM 就会把这个对象移动到 OldGeneration。
4.  JVM 提供了参数来设置分代年龄的大小，但是这个大小不能超过 15。
5. 不管这个对象的 gc 年龄是否达到了 15 次，只要满足动态年龄判断的依据，也同样会转移到 old generation。
  -  动态对象年龄判定：如果在Survivor空间中相同年龄所有对象大小的总和大于Survivor空间的一半，年龄大于或等于该年龄的对象就可以直接进入老年代，无须等到MaxTenuringThreshold中要求的年龄。
  
## 07 JVM中一次完整的GC流程  
1. 堆内存的组成：新生代和老年代组成，其中新生代又包括 Eden 区、Survivor 区[S0、S1]。
2.  Eden区空间满了以后，JVM 会触发一次 Minor GC，用来回收 Eden 区。
3. Eden 区存活下来的对象，会转移到 Survivor 区。如果对象是在 Eden 中出生，并经过第一次 Minor GC 后仍然存活，那这个时候，该对象的 GC 年龄会设置成 1
4. 每经过一次 Minor GC，该对象的 GC 年龄都会进行累加，如果超过默认的 GC 次数 15次，这个对象就会转移到老年代。
5. 当老年代满了无法容纳更多对象的时候，就会触发 Full GC，Full GC 会清理整个内存堆 包括年轻代、老年代。
6. 在进行 Full GC 之前，通常会先进行一次 Young GC，以尽可能地清理掉 Eden 区和 Survivor 区中的垃圾对象，以减少 Full GC 的压力和耗时。在 Young GC 之后，存活的对象将被复制到 Survivor区或 Old 区，而 Eden 区会被完全清空，等待下一次对象的分配。
7. Major GC 其实是 Full GC 的另外一种叫法。
  