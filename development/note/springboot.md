---
title: springboot随手记
description: 记录开发中遇到的或着解决的一些小问题，留用
published: true
date: 2023-05-30T08:55:08.671Z
tags: development, note
editor: markdown
dateCreated: 2023-05-30T08:55:08.671Z
---

# Springboot
Your content here

## springboot定制化tomcat参数
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

ServletWebServerFactoryAutoConfiguration