---
title: 【springCloudAlibaba-学习笔记】03-openFeign
description: 【springCloudAlibaba-学习笔记】03-openFeign
published: true
date: 2024-02-07T08:58:14.369Z
tags: springcloud alibaba, study
editor: markdown
dateCreated: 2024-02-07T08:58:14.369Z
---

## 004 openFeign
> springCloudAlibaba-学习笔记

[039-OpenFeign概述](https://www.bilibili.com/video/BV1VW4y1o7n5/?p=39)

[Spring Cloud OpenFeign](https://spring.io/projects/spring-cloud-openfeign)

> Declarative REST Client: Feign creates a dynamic implementation of an interface decorated with JAX-RS or Spring MVC annotations

- Feign:假装，伪装。`OpenFeign`可以将提供者提供的`Restful`服务伪装为接口进行消费，**消费者只需要使用feign接口 + 注解的方式即可直接调用提供者提供的`Restful`服务**，而无需在使用`RestTemplate`

- `JAX-RS`：Java api extensions of RESTful web services。

### OpenFeign总结：

1. 只涉及consumer，和provider无关。用于消费端调用提供端
2. 仅仅是一个伪客户端，不会对请求做任何的处理
3. 是通过注解方式实现Restful请求的

### OpenFeign 与 Ribbon

 OpenFeign具有负载均衡功能，其可以对指定的微服务采用负载均衡方式进行消费、访问。老版本的OpenFeign默认采用Ribbon负载均衡器。但是由于Netflix不再维护Ribbon，所有Springcloud2021.x开始集成OpenFeign中已彻底丢弃Ribbon，而是采用`SpringCloud`自行研发的`SpringCloud Loadbalancer`作为负载均衡器。

[Spring Cloud OpenFeign](https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/#netflix-feign-starter)

1. **添加依赖**

```xml
 <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
        
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>

```

2. **定义接口，controller调用**

```java
/**
 * 描述: feign接口， 参见提供端controller
 * @author Vic.xu
 * @date 2023-10-20 16:43
 */
@FeignClient(value = "depart-provider", path = "/provider/depart")
public interface DepartService {

    @PostMapping("/save")
    boolean save(@RequestBody Depart depart);

    @DeleteMapping("/delete/{id}")
    boolean delete(@PathVariable("id") int id);

    @PutMapping("/update")
    boolean update(@RequestBody Depart depart);

    @GetMapping("/get/{id}")
    Depart get(@PathVariable("id") int id);

    @GetMapping("/list")
    List<Depart> list();

}

```

3. 开启

> `@EnableFeignClients`



[一些配置：](https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/#spring-cloud-feign-overriding-defaults)

```properties
# default is global, can be changed to a real feignName
spring.cloud.openfeign.client.config.default.connectTimeout=5000
spring.cloud.openfeign.client.config.default.readTimeout=1
```


### [feign对请求和响应进行压缩](https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/#feign-requestresponse-compression)

```properties
spring.cloud.openfeign.compression.request.enabled=true
spring.cloud.openfeign.compression.request.mime-types=text/xml,application/xml,application/json
spring.cloud.openfeign.compression.request.min-request-size=2048
```



### OpenFeign 底层实现技术选型

- 默认`httpclient`：`spring.cloud.openfeign.httpclient.enabled=true`
- 支持httpclient5：`spring.cloud.openfeign.httpclient.hc5.enabled=false`
- 也支持`okhttp`: `spring.cloud.openfeign.okhttp.enabled=false`

feign 的远程调用底层实现技术默认采用的是`jdk`的`URLConnection`，同时还支持`Httpclient`和`Okhttp`。

 由于`jdk`的`URLConnection`不支持连接池，通信效率很低，所以生产中是不会使用该默认实现的，所在在spring cloud OpenFeign中直接将默认实现该为`HttpClient`，同是也支持`OkHttp`.



### 负载均衡以及策略更换

- `Client` feignClient: If Spring Cloud LoadBalancer is on the classpath, `FeignBlockingLoadBalancerClient` is used. If none of them is on the classpath, the default feign client is used.

- 加上`LoadBalancer`  依赖后默认负载均衡（`轮询`的方式）

#### eg: 配置随机策略：

- 启动类上加：`@LoadBalancerClients(defaultConfiguration = DepartConfig.class)`

- 编写随机策略：

  ```java
  public class DepartConfig {
      private static final Logger LOGGER = LoggerFactory.getLogger(DepartConfig.class);
  
      /**
       * 配置随机均衡策略
       */
      @Bean
      public ReactorLoadBalancer<ServiceInstance> randomLoadbalancer(Environment environment, LoadBalancerClientFactory factory){
          //获取负载均衡客户端名称，即提供者服务名称
          String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
          ObjectProvider<ServiceInstanceListSupplier> lazyProvider =
                  factory.getLazyProvider(name, ServiceInstanceListSupplier.class);
          LOGGER.info("获取负载均衡客户端名称，即提供者服务名称:{}", name);
          //从所有provider实例中指定名称的实例列表中随机选择一个实例
          // 参数1：获取指定名称的所有provider实例列表
          //参数2：指定要获取的provider实例名称
          return new RandomLoadBalancer(lazyProvider, name);
      }
  }
  ```

- `ReactorLoadBalancer`相关实现类：随机、轮询、nacos



#### 所以可以直接使用rpc框架：dubbo，均衡策略更丰富

 