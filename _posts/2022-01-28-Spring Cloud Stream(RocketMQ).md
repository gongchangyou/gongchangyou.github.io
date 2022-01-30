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

2. Spring-cloud-starter-stream-rocketmq 框架。spring-cloud-stream 集成多种MQ [https://spring.io/projects/spring-cloud-stream](https://spring.io/projects/spring-cloud-stream)

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





网上资料到处都是Output注解， 现在已经deprecated，目前推荐使用 StreamBridge

生产者

```
@Component
public class Producer {

    @Autowired
    private StreamBridge messageChannel;

    public boolean sendMsg(String msg) {
        return messageChannel.send("test-topic", MessageBuilder.withPayload(msg).build());
    }
}
```

问题1： Dispatcher has no subscribers 异常
![embbed]({{ site.baseurl}}/images/202201//Users/gongchangyou/Downloads/1643437768640.jpg)

## 这个问题没解决，考虑到未来也不太可能切换消息中间件，考虑直接使用 Rocketmq-spring-boot-starter 框架 吧。