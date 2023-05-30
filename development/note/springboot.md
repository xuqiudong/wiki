---
title: springboot随手记
description: 记录开发中遇到的或着解决的一些小问题，留用
published: true
date: 2023-05-30T09:00:10.776Z
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
  
  