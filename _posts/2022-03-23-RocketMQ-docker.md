---
layout: post
title: "rocketmq-docker"
date: 2022-03-23 14:25:06 +0800
comments: true
category: java
tag: [java]
---



#  RocketMQ docker 部署

基础术语：

1. 生产者(Producer)
2. 消费者(Consumer)
3. 主题（Topic)  一类消息的集合
4. 生产者组（Producer Group) 这类Producer 发送同一类消息。如果发送事务消息，且原始生产者在发送后崩溃，Broker Server 会联系同一组的生产者实例提交或回溯消费
5. 消费者组（Consumer Group) 消费同一类消息，注意同一个group的消费者必须订阅相同的topic。可以方便的负载均衡和容错
6. 代理服务器（Broker Server)  负责存储和转发消息，还存储元数据，包括消费者组、进度偏移、主题和队列消息
7. 名字服务器 （Name Server) 路由消息的提供者。 根据主题获取 Broker IP 的地址列表





参考 [https://www.jianshu.com/p/706588323276](https://www.jianshu.com/p/706588323276)

```
version: '3.5'
services:
  rmqnamesrv:
    image: foxiswho/rocketmq:server
    container_name: rmqnamesrv
    ports:
      - 9876:9876
    volumes:
      - ./data/logs:/opt/logs
      - ./data/store:/opt/store
    networks:
        rmq:
          aliases:
            - rmqnamesrv

  rmqbroker:
    image: foxiswho/rocketmq:broker
    container_name: rmqbroker
    ports:
      - 10909:10909
      - 10911:10911
    volumes:
      - ./data/logs:/opt/logs
      - ./data/store:/opt/store
      - ./data/brokerconf:/etc/rocketmq
    environment:
        NAMESRV_ADDR: "rmqnamesrv:9876"
        JAVA_OPTS: " -Duser.home=/opt"
        JAVA_OPT_EXT: "-server -Xms128m -Xmx128m -Xmn128m"
    #command: mqbroker -c /etc/rocketmq/broker.conf
    depends_on:
      - rmqnamesrv
    networks:
      rmq:
        aliases:
          - rmqbroker

  rmqconsole:
    image: apacherocketmq/rocketmq-dashboard
    container_name: rmqdashboard
    ports:
      - 8080:8080
    environment:
        JAVA_OPTS: "-Drocketmq.namesrv.addr=rmqnamesrv:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false"
    depends_on:
      - rmqnamesrv
    networks:
      rmq:
        aliases:
          - rmqconsole

networks:
  rmq:
    name: rmq
    driver: bridge
```



sudo vi ./data/brokerconf/broker.conf
```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.


# 所属集群名字
brokerClusterName=DefaultCluster

# broker 名字，注意此处不同的配置文件填写的不一样，如果在 broker-a.properties 使用: broker-a,
# 在 broker-b.properties 使用: broker-b
brokerName=broker-a

# 0 表示 Master，> 0 表示 Slave
brokerId=0

# nameServer地址，分号分割
# namesrvAddr=rocketmq-nameserver1:9876;rocketmq-nameserver2:9876

# 启动IP,如果 docker 报 com.alibaba.rocketmq.remoting.exception.RemotingConnectException: connect to <192.168.0.120:10909> failed
# 解决方式1 加上一句 producer.setVipChannelEnabled(false);，解决方式2 brokerIP1 设置宿主机IP，不要使用docker 内部IP
# brokerIP1=192.168.0.253

# 在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4

# 是否允许 Broker 自动创建 Topic，建议线下开启，线上关闭 ！！！这里仔细看是 false，false，false
autoCreateTopicEnable=true

# 是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true

# Broker 对外服务的监听端口
listenPort=10911

# 删除文件时间点，默认凌晨4点
deleteWhen=04

# 文件保留时间，默认48小时
fileReservedTime=120

# commitLog 每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824

# ConsumeQueue 每个文件默认存 30W 条，根据业务情况调整
mapedFileSizeConsumeQueue=300000

# destroyMapedFileIntervalForcibly=120000
# redeleteHangedFileInterval=120000
# 检测物理文件磁盘空间
diskMaxUsedSpaceRatio=88
# 存储路径
# storePathRootDir=/home/ztztdata/rocketmq-all-4.1.0-incubating/store
# commitLog 存储路径
# storePathCommitLog=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/commitlog
# 消费队列存储
# storePathConsumeQueue=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/consumequeue
# 消息索引存储路径
# storePathIndex=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/index
# checkpoint 文件存储路径
# storeCheckpoint=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/checkpoint
# abort 文件存储路径
# abortFile=/home/ztztdata/rocketmq-all-4.1.0-incubating/store/abort
# 限制的消息大小
maxMessageSize=65536

# flushCommitLogLeastPages=4
# flushConsumeQueueLeastPages=2
# flushCommitLogThoroughInterval=10000
# flushConsumeQueueThoroughInterval=60000

# Broker 的角色
# - ASYNC_MASTER 异步复制Master
# - SYNC_MASTER 同步双写Master
# - SLAVE
brokerRole=ASYNC_MASTER

# 刷盘方式
# - ASYNC_FLUSH 异步刷盘
# - SYNC_FLUSH 同步刷盘
flushDiskType=ASYNC_FLUSH

# 发消息线程池数量
# sendMessageThreadPoolNums=128
# 拉消息线程池数量
# pullMessageThreadPoolNums=128
```

<div style="color: #ff0000;">
	注意上面的配置：如果宿主机无法请求到broker， 是因为ip配置 <br>
    解决方式1: 加上一句 producer.setVipChannelEnabled(false);<br>
    解决方式2: brokerIP1 设置宿主机IP，不要使用docker 内部IP<br>

</div>
```
brokerIP1=192.168.0.253 
```


## 延迟消息

一般是在header中添加 delay 信息

{% highlight java %}
 /**
     * RabbitMQ header设置 x-delay 延迟5000ms
     * @param msg
     * @return
     */
    public boolean sendMsgDelay(String msg) {
        return messageSource.output1().send(MessageBuilder.withPayload(msg)
                        .setHeader("x-delay", 5000)
                .build());
    }

    /**
     * RocketMQ header设置 DELAY
     * 每个延迟级别对应的延迟时间可以通过配置messageDelayLevel
     * 默认值为“1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h”。
     * 该配置属于 Broker，对所有 Topic 有效！默认的level值为 0，代表非延迟消息，超过 18 按最大值 18 计算。
     * @param msg
     * @return
     */
    public boolean sendMsgDelayViaRocketMQ(String msg) {
        return messageSource.output1().send(MessageBuilder.withPayload(msg)
                .setHeader(MessageConst.PROPERTY_DELAY_TIME_LEVEL, 2)
                .build());
    }
    {% endhighlight %}





### ack机制

参考文章 : [https://www.baiyp.ren/RocketMQ%E6%B6%88%E8%B4%B9%E8%80%85%E4%BF%9D%E9%9A%9C.html](https://www.baiyp.ren/RocketMQ%E6%B6%88%E8%B4%B9%E8%80%85%E4%BF%9D%E9%9A%9C.html)

```
  <!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-stream-rocketmq -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rocketmq</artifactId>
            <version>2.2.7.RELEASE</version>
        </dependency>
```

RocketMQInboundChannelAdapter 这个类，注册监听事件有两个回调，一个失败，一个成功。 所以成功的话就自动ack了,失败会重新投递.  
![]({{ site.baseurl}}/images/202203/WechatIMG62.png){: width="800" }
那何时算失败呢？ 那就是在消费中抛出任何异常就算失败了。
![]({{ site.baseurl}}/images/202203/WechatIMG63.png){: width="800" }



在这个例子中 我做了个NPE，所以就无限reconsume了.

![]({{ site.baseurl}}/images/202203/WechatIMG63.png){: width="800" }