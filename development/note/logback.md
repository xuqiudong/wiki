---
title: logback格式随手记
description: 
published: true
date: 2024-02-06T06:30:34.976Z
tags: development, note, log
editor: markdown
dateCreated: 2024-02-06T06:30:34.976Z
---

# logback格式随手记
 http://logback.qos.ch/manual/layouts.html
## 一、pattern日志格式之一
### 常用：

%c 输出logger名称
%C 输出类名
%d{HH:mm:ss.SSS} 表示输出到毫秒的时间
%t 输出当前线程名称
%-5level 输出日志级别，-5表示左对齐并且固定输出5个字符，如果不足在右边补0
%logger 输出logger名称，因为Root Logger没有名称，所以没有输出
%msg 日志文本
%n 换行

### 其他常用的占位符有：
%F 输出所在的类文件名，如Log4j2Test.java
%L 输出行号
%M或%method 输出所在方法名
%l 输出完整的错误位置, 包括类名、方法名、文件名、行数
%p 该条日志的优先级
%replace{pattern}{regex}{substitution} 将pattern的输出结果pattern按照正则表达式regex替换成substitution

## 二、pattern日志格式之二
### 变量名：
%logger（简写：%c或%lo）: 当前日志名称，如: Slf4jAndLogbackMain
%class（简写：%C）: 日志调用所在类，如: com.dragon.study.log.Slf4jAndLogbackMain
%method（简写：%M）: 日志所在方法，如: main
%caller: 日志调用位置，如：at com.dragon.study.log.Slf4jAndLogbackMain.main(Slf4jAndLogbackMain.java:15)
%thread（简写：%t）: 日志调用所有线程序,如：main
%level（简写：%p或%le）: 日志级别，如：INFO
%date（简写：%d）: 日期，如: 2018-12-15 21:40:12,890
%msg（简写：%m）: 日志记录内容
%exception（简写：%ex）: 异常记录

### 宽度设置：
%20logger:当字符数少于20个字符时，则左侧留空白;
%-20logger:当字符数少于20个字符时，则右侧留空白;
%.30logger:当字符数据大于30个时，则截断;

### 显示设置：
%highligth:突出显示
%green(%red、%blue、%white)：字体显示为指定颜色
{length}可指定长度，如%logger{36}

### 网络访问设置（依赖logger-access包）：
%remoteIP（简写：%a）:远程ip;
%localIP（简写：%A）:本地ip;
%clientHost（简写：%h）:远程主机名;
%localPort:本地端口;
%requestMethod（简写：%m）:http请求方法;
%protocol（简写：%H）:http请求协议;
%statusCode（简写：%s）:http请求status code;
%requestURL（简写：%r）:http请求地址;
%requestURI（简写：%U）:http请求资源地址;
%queryString（简写：%q）:http请求参数;
%server（简写：%v）:服务器地址;
%elapsedTime（简写：%D）:http请求处理的时间，单位是毫秒;
%elapsedSeconds（简写：%T）:http请求处理的时间，单位是秒;
%date（简写：%t）:日志记录时间;
%threadName（简写：%I）:处理请求的线程名;
%reqAttribute{attributeName}:http请求attribute值;
%reqCookie{cookie}:http请求cookie值;
%reqContent:http请求体内容;
%fullRequest:http完整请求;
%responseContent:http响应;
%fullResponse:http完整响应;