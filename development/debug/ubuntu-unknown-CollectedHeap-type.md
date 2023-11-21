---
title: Ubuntu jmap -heap 命令出现:unknown CollectedHeap type : class sun.jvm.hotspot.gc_interface.CollectedHeap
description: Ubuntu jmap -heap 命令出现:unknown CollectedHeap type : class sun.jvm.hotspot.gc_interface.CollectedHeap
published: true
date: 2022-07-22T01:59:33.029Z
tags: debug, development
editor: markdown
dateCreated: 2022-07-22T01:56:44.507Z
---

###  Ubuntu 系统下运行jmap -heap pid 命令出现如下错误:unknown CollectedHeap type

```
Heap Usage:
Exception in thread "main" java.lang.reflect.InvocationTargetException
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at sun.tools.jmap.JMap.runTool(JMap.java:201)
        at sun.tools.jmap.JMap.main(JMap.java:130)
Caused by: java.lang.RuntimeException: unknown CollectedHeap type : class sun.jvm.hotspot.gc_interface.CollectedHeap
        at sun.jvm.hotspot.tools.HeapSummary.run(HeapSummary.java:144)
        at sun.jvm.hotspot.tools.Tool.startInternal(Tool.java:260)
        at sun.jvm.hotspot.tools.Tool.start(Tool.java:223)
        at sun.jvm.hotspot.tools.Tool.execute(Tool.java:118)
        at sun.jvm.hotspot.tools.HeapSummary.main(HeapSummary.java:49)
        
```        
### 原因:没有安装openjdk-debug

1. 查找源：sudo apt-cahce search openjdk
2. 安装查到的源 `openjdk-8-dbg `: sudo apt install openjdk-8-dbg
3. 再次运行 jmap -heap pid  可顺利展示堆内存信息

```

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 2042626048 (1948.0MB)
   NewSize                  = 42467328 (40.5MB)
   MaxNewSize               = 680525824 (649.0MB)
   OldSize                  = 85458944 (81.5MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 217579520 (207.5MB)
   used     = 164886464 (157.24798583984375MB)
   free     = 52693056 (50.25201416015625MB)
   75.7821618505271% used
From Space:
   capacity = 16252928 (15.5MB)
   used     = 15868136 (15.133033752441406MB)
   free     = 384792 (0.36696624755859375MB)
   97.63247582220262% used
To Space:
   capacity = 20447232 (19.5MB)
   used     = 0 (0.0MB)
   free     = 20447232 (19.5MB)
   0.0% used
PS Old Generation
   capacity = 130547712 (124.5MB)
   used     = 29376592 (28.015701293945312MB)
   free     = 101171120 (96.48429870605469MB)
   22.502571320438% used

28075 interned Strings occupying 2757984 bytes.

```