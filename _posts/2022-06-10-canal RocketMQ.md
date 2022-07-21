---
layout: post
title: "canal RocketMQ"
date: 2022-06-10 10:25:06 +0800
comments: true
category: java
tag: [java, rocketmq, mysql]


---

# canal 导入RocketMQ

项目地址： [https://github.com/alibaba/canal/](https://github.com/alibaba/canal/)



[启动RocketMQ]({{ site.baseurl}}/2022/03/RocketMQ-docker)

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
#这个是mq的主题，这个保证和rocketMQ的消费端的主题一致即可
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



![]({{ site.baseurl}}/images/202206/WechatIMG312.png){: width="800" }

1.  红色圈 data中是完整的一行新值
2.  绿色的是主键id
3.  蓝色的那个只是消息自增id，不是主键
4.  old 中的是这次修改的字段的旧值

---



如何实现动态主题？

注意到 类 CanalRocketMQProducer 中的 send方法 ,需要设置一个 dynamicTopic的配置

![]({{ site.baseurl}}/images/202207/WechatIMG345.png){: width="800" }

```
canal.mq.topic=test-topic
# dynamic topic route by schema or table regex
canal.mq.dynamicTopic=mytest1.user,topic2:mytest2\\..*,.*\\..*
```

其中  
```
mytest1.user 表示 mytest1db user 表 发送到主题为 mytest1_user 中

topic2:mytest2\\..* 表示  mytest2的所有表的操作 都发送到主题  topic2

.*\\..*  表示匹配所有db所有table  并发送到 db_table 的主题中
```
注意： 如果 没匹配到 动态主题的话，会选择 默认的 canal.mq.topic 主题发送



![]({{ site.baseurl}}/images/202207/WechatIMG346.png){: width="800" }

参考文章: [https://blog.csdn.net/god8816/article/details/117308235](https://blog.csdn.net/god8816/article/details/117308235)



问题1：

Caused by: org.springframework.beans.factory.support.BeanDefinitionOverrideException: Invalid bean definition with name 'test-topic.group.errors.recoverer' defined in null: Cannot register bean definition [Generic bean: class [org.springframework.integration.handler.advice.ErrorMessageSendingRecoverer]; scope=singleton; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null] for bean 'test-topic.group.errors.recoverer': There is already [Generic bean: class [org.springframework.integration.handler.advice.ErrorMessageSendingRecoverer]; scope=singleton; abstract=false; lazyInit=null; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null] bound.



回答1：不同topic的 group必须设置成不同的. 具体可以看 MQClientInstance.registerConsumer 方法，consumerTable 是一个group对应一个consumer的map.

```
nested exception is org.apache.rocketmq.client.exception.MQClientException: The consumer group[group] has been created before, specify another name please.
See http://rocketmq.apache.org/docs/faq/ for further details.
```



![]({{ site.baseurl}}/images/202207/WechatIMG347.png){: width="800" }



在BindingService这里有个递归，但其实递归不了，会抛上述异常。因为 ErrorMessageSendingRecoverer 已经实例化过了。

![]({{ site.baseurl}}/images/202207/WechatIMG348.png){: width="800" }