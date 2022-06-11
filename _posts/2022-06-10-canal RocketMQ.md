---
layout: post
title: "canal RocketMQ"
date: 2022-06-09 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# canal 导入RocketMQ

项目地址： [https://github.com/alibaba/canal/](https://github.com/alibaba/canal/)



[启动RocketMQ]({{ site.baseurl}}/2022/06/canal)

[部署canal]({{ site.baseurl}}/2022/06/canal)



修改源码的canal.properties ，但是没生效

![]({{ site.baseurl}}/images/202206/WechatIMG268.png){: width="800" }



这些实现类是用SPI加载的

CanalLauncher.java -》 CanalStarter.java

追进去看到 classLoader 会去 target/plugin 文件夹中读取class。找不到再走META-INF/canal/xxx.CanalMQProducuer

![]({{ site.baseurl}}/images/202206/WechatIMG269.png){: width="800" }



把如下注释掉就行了

![]({{ site.baseurl}}/images/202206/WechatIMG270.png){: width="800" }



在canal源码中的配置

```
#这个不用动
canal.destinations = example
#这个是mq的主题，这个保证和下图的主题一致即可
canal.mq.topic=test-topic

#模式从 tcp 改成 rocketMQ
canal.serverMode = rocketMQ

```

在消费端的rocketMQ的配置

```
spring.cloud.stream.rocketmq.binder.name-server=127.0.0.1:9876
spring.cloud.stream.bindings.input1.destination=test-topic
spring.cloud.stream.bindings.input1.content-type=application/json
spring.cloud.stream.bindings.input1.group=group
```



成功！

```
2022-06-11 10:11:29.366  INFO 2493 --- [MessageThread_1] com.mouse.canal_test.rocketmq.Consumer   : payload={"data":[{"id":"5","count":"13"}],"database":"store_db","es":1654874446000,"id":1,"isDdl":false,"mysqlType":{"id":"int","count":"int"},"old":[{"count":"313"}],"pkNames":["id"],"sql":"","sqlType":{"id":4,"count":4},"table":"store","ts":1654911313692,"type":"UPDATE"}

```



