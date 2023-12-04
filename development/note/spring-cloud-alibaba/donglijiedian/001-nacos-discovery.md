---
title: 【spring-cloud-albaba】001-Nacos服务注册与发现
description: Nacos服务注册与发现
published: true
date: 2023-12-04T09:43:11.286Z
tags: springcloud, springcloud alibaba, 动力节点, 学习笔记
editor: markdown
dateCreated: 2023-12-04T09:43:11.286Z
---

# 001-Nacos服务注册与发现
- 代码仓库： [springcloud-alibaba branch](https://gitee.com/xuqiudong/code-learning/tree/springcloud-alibaba/)

> 常见：zk、eureka、consul、nacos。

[Nacos 快速开始](https://nacos.io/zh-cn/docs/quick-start.html)

https://www.bilibili.com/video/BV1VW4y1o7n5/?p=15



- nacos：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

- 云原生应用简单来说就是SaaS(软件即服务)，就是跑在laaS(基础设施即服务)、PaaS(平台即服务)上的SaaS。
- 云与安生 = 微服务+ DevOps + cd + 容器化。

[016-Nacos系统架构解析](https://www.bilibili.com/video/BV1VW4y1o7n5/?p=16&spm_id_from=pageDriver&vd_source=80427d8d349fbb75d43d4f5000a0c454) 



[版本对应](https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E)：

- springcloud alibaba 2022.0.0.0.0  对应nacos2.2.1

nacos安装参照官网。



添加依赖：https://spring.io/projects/spring-cloud-alibaba

```xml
<dependencyManagement>
    <dependencies>
         <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2022.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2022.0.0.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

 [019-将consumer注册到nacos_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1VW4y1o7n5/?p=19&spm_id_from=pageDriver&vd_source=80427d8d349fbb75d43d4f5000a0c454)

```xml
  <!--nacos  discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>
```



```java
  /**
     * 以负载均衡的方式调用  新版不在支持ribbon的负载均衡  ,所以需要加入spring-cloud-starter-loadbalancer 依赖
     */
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
```

### 代码获取nacos注册表信息

```java
     //获取所有服务名称
        List<String> services = discoveryClient.getServices();
        System.out.println("into  discovery, services size = " + services.size() + "" );
        for (String service : services) {
            //获取指定服务的实例
            List<ServiceInstance> instances = discoveryClient.getInstances(service);
            for (ServiceInstance instance : instances) {
                Map<String, Object> map = new HashMap<>();
                map.put("instanceId", instance.getInstanceId());
                map.put("serviceId", instance.getServiceId());
                map.put("host", instance.getHost());
                map.put("port", instance.getPort());
                map.put("uri", instance.getUri());
                System.out.println(map);
            }
        }
```



### 注册表缓存

- 服务启动后，当**发生调用时**，会自动从ancos注册中心下载并缓存注册表到本地。

- 所以，当nacos宕机，消费者仍然可以调用到提供者。
- 只不过此时不再能进行服务注册了，缓存的注册列表信息也无法更新。4

### 临时实例与持久实例

#### 区别

- **临时实例**：默认情况下，服务实例只会注册在nacos内存，不会持久化到nacos磁盘。其健康监测机制为client模式，即**client主动向server上报其健康状态**。在30s内若收到client心跳，则重新恢复健康状态。否则该实例将从server端内存清除。
- **持久实例**：服务实例不仅会注册到nacos内存，同时会持久化到nacos磁盘，其健康监测机制会server模式，即**server会主动监测client的健康状态**。默认每20s监测一次，健康监测失败后服务实例会被标记为“不健康”状态，但不会清除，因为其是持久化在磁盘的。

一旦注册为临时实例，就不能出则为持久实例，除非把磁盘上的删除或改名，默认在用户目录下的`/naming/public/`下

```properties
# ephemeral default is true 
spring.cloud.nacos.discovery.ephemeral=false
```



**注销实例接口**：[Open API 指南 (nacos.io)](https://nacos.io/zh-cn/docs/open-api.html)

```
curl -X DELETE '127.0.0.1:8848/nacos/v1/ns/instance?serviceName=depart-consumer&ip=192.168.140.121&port=8080&username=nacos&password=nacos12345678'
```



### 数据持久化到mysql

```sql
 CREATE DATABASE nacos_config DEFAULT CHARSET utf8;
-- GRANT ALL PRIVILEGES ON nacos_config.* TO 'nacos'@'localhost' IDENTIFIED BY '12345678';
 -- mysql8
 CREATE USER  'nacos'@'localhost' IDENTIFIED BY '12345678';
 GRANT ALL PRIVILEGES ON nacos_config.* TO 'nacos'@'localhost' WITH GRANT OPTION;

 FLUSH  PRIVILEGES;

```

- mysql-schema.sql

```properties
spring.sql.init.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=nacos
db.password.0=12345678
```



### [nacos 集群搭建与CAP](https://www.bilibili.com/video/BV1VW4y1o7n5/?p=25) ★

[集群部署说明 (nacos.io)](https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html)

1. cluster.conf 下配置nacos集群ip列表：端口不可连续;然后分别以集群模式启动

```
192.168.140.121:8849
192.168.140.121:8851
192.168.140.121:8853
```

2. springcloud连接（可以修改为nginx代理）

`spring.cloud.nacos.discovery.server-addr=localhost:8849,localhost:8851,localhost:8853`



报错：[The Raft Group [naming_instance_metadata\] did not find the Leader node;](https://www.cnblogs.com/whl-jx911/p/16736625.html)

> 解决方案
> Nacos 采用 raft 算法来计算 Leader，并且会记录上次启动的集群地址，所以当我们自己的服务器 IP 改变时(网络环境不稳定，如WIFI， IP 地址也经常变化)，导致 raft 记录的集群地址失效，导致选 Leader 出现问题，方法：删除 Nacos 根目录下 data 文件夹下的 protocol 文件夹即可。
>
> 原因：有小伙伴，部署的nacos包，是本机运行过的，直接扔到服务器了(带着data目录)，所以ip 与服务器ip不一致，导致了以上问题。



#### nacos的CAP模式

- 默认情况下nacos discovery集群的数据一致性采用的是AP模式，但也支持CP模式。
- 若需要转换为CP模式，需要提交put请求完成



#### nacos中的数据模型

[Nacos 架构](https://nacos.io/zh-cn/docs/architecture.html)

- Nacos中的服务是由三元组唯一确定的：namespace、group和服务名称service。
- namespace与group的作用是ixiangtong，用于划分不同的区域范围，隔离服务。不同的是，namespace的范围更大，不同的namespace中可以包含相同的group，不同的group中可以包含相同的service。

- **Namespace**默认是**空串**，公共命名空间（**public**），分组默认是 **DEFAULT_GROUP**。

- `NameSpace`   >>  `group` >> `service/DataId`

**命名空间是需要创建的。**



#### 服务隔离

只能消费同namespace下的同group内的服务

 