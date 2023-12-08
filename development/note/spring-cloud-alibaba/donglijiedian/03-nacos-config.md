---
title: 【spring-cloud-alibaba】【动力节点】02-nacos服务配置中心
description: nacos服务配置中心
published: true
date: 2023-12-08T09:26:53.135Z
tags: springcloud alibaba, 动力节点, 学习笔记
editor: markdown
dateCreated: 2023-12-08T09:23:11.545Z
---

## 02-nacos cofig服务配置中心

  集群中每台主机的配置文件都是相同，对配置文件的更新维护，统一交给配置中心管理。

- `spring cloud config`: 
  - 配置文件放在远程库(github)，手动维护
  - config server：和远程库通讯
  - 消息总线系统   spring cloud bus + mq(kafka/rebbitMq)
  - client 发出bus-refresh请求，从远程库请求配置更新，并发给总线，广播给其他client，更新配置。
  - **存在的问题：**
    - 无法自动感知更新；
    - 羊群效应，一个更新，全部更新（性能）
    - 架构比较复杂，成本高，难维护。
- `apollo`：
  - 配置文件放在数据库
  - admin service（eureka client）：操作控制台修改数据库
  - config service（eureka client）：拉取数据库配置
  - Eureka server：注册中心管理上述service
  - meta server：作为eureka client
  - config client：连接到meta server
  - 可以自动感知噢诶之文件的更新，但存在不足：
    - 系统架构复杂
    - 配置文件支持类型较少，只支持xml/text/properties
- `zookeeper`：
  - 数据放在zk自身。
  - watcher实现自动更新感知。
- `disconf`(百度，不再维护)：
- `nacos`

### nacos配置中心原理

- 只支持mysql;实时更新；

- config server（nacos）
- config client: 自动感知配置中心相应配置文件的更新。
- 架构简单，支持的文件类型较多。



### 数据一致性问题 ★

#### 配置中心

- **配置中心**的配置数据一般都是持久化在第三方服务器的，如db，git等。
- 这些配置中心server中不存放数据，所以在它们的集群中就不存在数据一致性。
- 但zookeeper作为配置中心，数据是存于自身的，所以集群中的节点存在数据一致性问题
  - zk集群对于数据一致性是采用CP模型的：即更新数据的的时候，短时间内是不可用的。

#### 注册中心

- 作为**注册中心**，这些server集群间是存在数据一致性问题的：zk(CP)，eureka(AP)、consul(AP)采用的模式是不同的。nacos 默认AP，也可以CP。

nacos  [get-config] get from server error  401

Client not connected, current status:STARTING



#### 

[034-provider从配置中心读取配置文件](https://www.bilibili.com/video/BV1VW4y1o7n5/?p=34&spm_id_from=pageDriver&vd_source=80427d8d349fbb75d43d4f5000a0c454)

spring cloud 从2021开始已经不再扫描`bootstrap.properties`了

新版本直接使用application配置文件

```yaml
spring:
  application:
    name: depart-provider

  cloud:
    nacos:
      config:
        server-addr: localhost:8848
        file-extension: yml
        username: nacos
        password: nacos12345678
        # 共享的配置文件：只有同一个组下同一个group
        #shared-configs:shared-config.yml,shared-config2.yml
        shared-configs:
          - dataId: shared-config.yml
            refresh: true
        # 扩展配置： 
        extension-configs:
          - dataId: shared-config2.yml
            refresh: true


  config:
    import:
#      - optional:nacos:depart-provider.yml
      - optional:nacos:${spring.application.name}.${spring.cloud.nacos.config.file-extension}
```

#### 关于配置文件的扩展

- 当前服务配置、共享配置与扩展配置的加载顺序：
  - **加载顺序：共享配置 → 扩展配置 → 当前配置**
  - 具有相同key，后加载的会覆盖先加载的。
  - **优先级：当前配置→ 扩展配置 → 共享配置**
- 当前服务配置可以存在三个地方：
  1. **快照**：当前用户/nacos/config/下：远程下载到本地缓存到本地快照
  2. **本地配置主动存放**：也可以主动存放上面config/.../data/config-data/{group}/{application-name.yml}
  3. 远程nacos中的配置文件
  4. 上述三个同名文件的加载顺序：本地、远程、快照；只要系统加载到了配置文件，后面的配置文件就不会再加载了

#### 配置的动态更新

- 在`@Value`注入的类上加上 `@RefreshScope`注解

#### 多环境选择的实现

- 配置(克隆)多环境配置文件：depart-provider-dev.yml，depart-provider-test.yml

```yaml
spring:   
  profiles:
    active: test

  config:
    import:
      - optional:nacos:${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}
```

#### 配置文件隔离

- 指定group
- 指定namespace
- 指定dataId

