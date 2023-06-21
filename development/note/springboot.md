---
title: springboot随手记
description: 记录开发中遇到的或着解决的一些小问题，留用
published: true
date: 2023-06-21T09:37:10.505Z
tags: development, note
editor: markdown
dateCreated: 2023-05-30T08:55:08.671Z
---

# Springboot
 

## 001. springboot定制化tomcat参数
  springboot中内置了很多tomcat的参数,参见
  `spring-configuration-metadata.json`和 [springboot官网](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#appendix.application-properties.server)中`server.tomcat`前缀的一些配置。
  
  不过有些参数没有，比如`maxParameterCount`这个参数的配置，所以可以自定义加入想加入的参数到内嵌的tomcat中。
  配置个
  
```
@Bean
public TomcatConnectorCustomizer tomcatConnectorCustomizer(){
	
	return new TomcatConnectorCustomizer(){
     @Override
     public void customize(Connector connector) {
        connector.setPort(9090);
        connector.setMaxParameterCount(20000);
    }
  }
}
```
spingboot2.7.x源码参见：
- ServletWebServerFactoryAutoConfiguration → 
- EmbeddedTomcat:
 
 ```
  @Bean 
  TomcatServletWebServerFactory tomcatServletWebServerFactory(
				ObjectProvider<TomcatConnectorCustomizer> connectorCustomizers,
				ObjectProvider<TomcatContextCustomizer> contextCustomizers,
				ObjectProvider<TomcatProtocolHandlerCustomizer<?>> protocolHandlerCustomizers) {
			TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
			factory.getTomcatConnectorCustomizers()
					.addAll(connectorCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getTomcatContextCustomizers()
					.addAll(contextCustomizers.orderedStream().collect(Collectors.toList()));
			factory.getTomcatProtocolHandlerCustomizers()
					.addAll(protocolHandlerCustomizers.orderedStream().collect(Collectors.toList()));
			return factory;
 }
 ```
  
[SpringBoot tomcat定制化实现原理](https://blog.csdn.net/guiyiba/article/details/121785184)
  
## 002 springboot无法获取nginx代理的scheme
- nginx版本:1.18+
- springboot版本：2.7.3
> 问题描述：nginx配置https后，springboot从request中获取的schema依然是http而不是https。

**解决方案：**
1. **nginx设置代理头，转发真是请求头信息**
```
  proxy_set_header   X-Real-IP        $remote_addr;
	proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
	proxy_redirect http:// https://;  # or $scheme
	proxy_set_header   X-Forwarded-Proto  https; #or $scheme
	proxy_set_header   Host $http_host;
```
2. **设置springboot2.7.3中的tomcat参数**
 [参见官网：17.3.12. Running Behind a Front-end Proxy Server](https://docs.spring.io/spring-boot/docs/2.7.3/reference/htmlsingle/#howto.webserver.use-behind-a-proxy-server)
```
server.forward-headers-strategy=NATIVE
server.tomcat.redirect-context-root=false
```

在之前的版本2.1.x中可以添加配置
[Enable HTTPS When Running behind a Proxy Server](https://docs.spring.io/spring-boot/docs/2.1.x/reference/html/howto-security.html)
```
server.tomcat.remote-ip-header=x-forwarded-for
server.tomcat.protocol-header=x-forwarded-proto
```