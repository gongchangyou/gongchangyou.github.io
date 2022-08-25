---
layout: post
title: "spring cloud stream"
date: 2022-01-28 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring Cloud Stream  & RocketMQ

## RocketMQ有两种实现方式

1. Rocketmq-spring-boot-starter 框架 (rocketmq && spring-boot)
2. Spring-cloud-starter-stream-rocketmq 框架。 推荐使用这种，因为spring-cloud-stream 集成多种MQ [https://spring.io/projects/spring-cloud-stream](https://spring.io/projects/spring-cloud-stream)



我选择版本2.2.7.RELEASE: [https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-stream-rocketmq](https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-stream-rocketmq)

注意处理冲突和添加依赖，我这边缺少了  spring-boot-actuator-autoconfigure
```
  <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-actuator-autoconfigure -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-actuator-autoconfigure</artifactId>
            <version>2.3.12.RELEASE</version>
        </dependency>
```

  sample [https://github.com/spring-cloud/spring-cloud-stream-samples](https://github.com/spring-cloud/spring-cloud-stream-samples)



生产者 和消费者，懒得新增仓库了。直接写在了原来的dubbo demo  中.

[https://github.com/gongchangyou/dubbo-demo-producer](https://github.com/gongchangyou/dubbo-demo-producer)

[https://github.com/gongchangyou/dubbo-demo-consumer](https://github.com/gongchangyou/dubbo-demo-consumer)

代码很简单
![embbed]({{ site.baseurl}}/images/202201/1643521131415.jpg){: width="1000" }
![embbed]({{ site.baseurl}}/images/202201/1643521145955.jpg){: width="1000" }
![embbed]({{ site.baseurl}}/images/202201/1643521166242.jpg){: width="1000" }

配置
```
spring.cloud.stream.rocketmq.binder.name-server=127.0.0.1:9876
spring.cloud.stream.bindings.output1.destination=test-topic #主题 topic
spring.cloud.stream.bindings.output1.content-type=application/json

```



## 消费者

配置

```
spring.cloud.stream.rocketmq.binder.name-server=127.0.0.1:9876
spring.cloud.stream.bindings.input1.destination=test-topic
spring.cloud.stream.bindings.input1.content-type=application/json
spring.cloud.stream.bindings.input1.group=group
```


代码

![embbed]({{ site.baseurl}}/images/202201/1643523658805.jpg){: width="1000" }
![embbed]({{ site.baseurl}}/images/202201/1643523559576.jpg){: width="1000" }
![embbed]({{ site.baseurl}}/images/202201/1643523584792.jpg){: width="1000" }


调试ok

