---
title: 架构风格汇总
description: 架构风格汇总，很重要
published: true
date: 2022-06-28T00:32:15.956Z
tags: 学历, 软考
editor: markdown
dateCreated: 2022-06-28T00:32:15.956Z
---

 
#  数据流
## 数据流：批处理
> 一个接一个，**以整体为单位**
## 数据流：管道-过滤器
> 一个接一个，前一个输出是后一个输入

> 两者常考： **传统编译器**，每个阶段产生的结果做为下一个阶段的输入，区别在于整体。

# 调用/回
## 调用/返回：主程序-子程序
> 显式调用，主程序直接调用子程序
## 调用/返回：面向对象
> 对象是构件，通过对象调用封装的方法和属性。
## 调用/返回：层次结构
> 分层，每层最多影响其上下两层，有调用关系。

# 独立构件
## 独立构件：进程通信
> 进程间独立的消息传递，同步异步。
## 独立构件：事件驱动(隐式调用)
> 不直接调用，通过事件驱动。

> 常考： 事件触发推动动作，如**程序语言的语法高亮、语法错误提示**。

# 虚拟机
## 虚拟机： 解释器
> 解释自定义规则，解释引擎、存储区、数据结构。
## 解释器：规则系统
> 规则集、规则解释器、选择器和工作内存，用于**DSS和人工智能、专家系统**。

> 二者考点：**自定义流程**，按流程执行，规则随时改变，**灵活定义**，业务灵活组合。**机器人**。 

# 仓库
## 仓库：数据库
> 中央共享数据源，独立处理单元。
## 仓库：超文本
> 网状链接， 多用于互联网。
## 仓库： 黑板
> **语音识别、知识推理**等问题复杂、解空间很大、求解过程不确定的这一类软件系统，黑板、知识源、控制。

> 三者考点：现代编译器的**集成开发环境IDE**，以数据为中心。又称为**数据共享风格**。

# 闭环：
## 闭环： 过程控制
> 发出控制命令并接受反馈，循环往复达到平衡。

> 考点： **汽车巡航定速，空凋温度调节**，设定参数，并不断调整。

# C2风格
> 通过连接件绑定在一起按照一规则运作的并行构件网络。

> 考点： **构件和连接件、顶部和底部**
