---
layout: post
title: "slf4j"
date: 2022-02-08 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  slf4j & logback

slf4j是基于门面模式的日志模块，logback是其中的一个实现，另个常用的是log4j

去年log4j 的漏洞整怕了，还是用logback实现吧。



有几个常用的slf4j和logback相关模块 

1. logback-classic 日志的实现 ，内置logback-core

2. logback-access [https://logback.qos.ch/access.html#configuration](https://logback.qos.ch/access.html#configuration) 可以在 *$TOMCAT_HOME/conf/server.xml* 中添加 

   ```
   <Connector port="8050" 
     handler.list="mx"
     mx.enabled="true" 
     mx.httpHost="localhost" 
     mx.httpPort="8082" 
     protocol="AJP/1.3" />
   ```

   访问模块与 Servlet 容器集成提供通过 Http 来访问日记的功能。

3. logstash-logback-encoder 以json格式来保存日志
4. lombok 可以使用@Slf4j注解，避免每个类中都写LoggerFactory

Logback-spring.xml
```
<?xml version="1.0" encoding="utf-8" ?>

<configuration debug="false">
    <!-- 控制台输出 -->
    <conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter"/>
    <conversionRule conversionWord="wex"
                    converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter"/>
    <conversionRule conversionWord="wEx"
                    converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter"/>

        <!-- 按照每天生成日志文件 -->
        <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <file>${LOGS_DIR}/service.log</file>
            <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
                <!--日志文件输出的文件名-->
                <FileNamePattern>${LOGS_DIR}/service.%d{yyyy-MM-dd}.%i.log</FileNamePattern>
                <!--日志文件保留天数-->
                <maxFileSize>5GB</maxFileSize>
            </rollingPolicy>
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}┇%-5level┇[%thread]┇%logger{50}┇%msg%n</pattern>
            </encoder>
            <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
                <!-- 在日志配置级别的基础上过滤掉warn级别以下的日志 -->
                <level>WARN</level>
            </filter>
        </appender>

        <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}┇%-5level┇[%thread]┇%logger{50}┇%msg%n</pattern>
            </encoder>
        </appender>

    <springProfile name="dev">
        <root level="INFO">
            <appender-ref ref="STDOUT"/>
            <appender-ref ref="FILE"/>
        </root>
    </springProfile>

</configuration>

```

记得在 application.properties中添加如下参数，或者启动项中添加，这样springProfile能生效

```
spring.profiles.active=dev
```

