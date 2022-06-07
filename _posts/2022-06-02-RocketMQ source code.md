---

layout: post
title: "RocketMQ source code"
date: 2022-06-02 10:25:06 +0800
comments: true
category: rocketmq
tag: [rocketmq]

---

# RocketMQ 源码解析

参考文章: [https://juejin.cn/post/6847902221577748487](https://juejin.cn/post/6847902221577748487)

代码仓库： [https://github.com/gongchangyou/dubbo-demo-producer](https://github.com/gongchangyou/dubbo-demo-producer)

[https://github.com/gongchangyou/dubbo-demo-consumer](https://github.com/gongchangyou/dubbo-demo-consumer)

这里借用了dubbo的demo， 所以可能需要启动nacos. 





### netty基础知识:
RocketMQ是的TCP通信基于 netty的
初始化时, NettyRemotingClient.start 中， 实例化一个bootstrap, 设置一个 ChannelHandler，这个handler给pipeline注册一堆 ChannelHandler，比如 SimpleChannelInboundHandler， 可以read channel中的报文。

![]({{ site.baseurl}}/images/202206/WechatIMG256.png){: width="800" }

### 生产端 producer
![]({{ site.baseurl}}/images/202206/producer_uml.drawio.png){: width="800" }

---
### 消费端 consumer

参考文章: [https://blog.csdn.net/weixin_34452850/article/details/82712634](https://blog.csdn.net/weixin_34452850/article/details/82712634)

长轮询上面这篇文章介绍过了，具体方法就是 DefaultMQPushConsumerImpl.executePullRequestLater

<font color="red">管理类是 MQClientInstance，一个主题对应一个，里面有很多成员，管理主题路由信息（topicRouteTable），消费者(consumerTable) 等 </font>


consumer的设计简直回调地狱.



![]({{ site.baseurl}}/images/202206/consumer_uml.drawio.png){: width="800" } 
![]({{ site.baseurl}}/images/202206/consumer_receive_uml.drawio.png){: width="800" } 



1.  consumer是利用 PullMessageService 中的 LinkedBlockingQueue<PullRequest> pullRequestQueue 来获取消息. 因为producer的这个 pullRequestQueue 不会put元素进去，所以会阻塞在这个take方法，也不会有啥影响，顶多就是占用一个空的queue对象.

    ![]({{ site.baseurl}}/images/202206/WechatIMG257.png){: width="800" }



2.   这个 schedule task 里面启动了很多轮询任务，包括更新主题路由信息(根据topic 获取addr)，清理掉线的broker，上报offset给broker，心跳检测。

```
this.startScheduledTask();

private final ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor(new ThreadFactory() {
        @Override
        public Thread newThread(Runnable r) {
            return new Thread(r, "MQClientFactoryScheduledThread");
        }
    });
scheduledThreadPoolExecutor.scheduleAtFixedRate
```

---

### 核心组件 broker 
这个我说不好，暂时没时间看源码了，参考文章:

[https://juejin.cn/post/6844904191916245000](https://juejin.cn/post/6844904191916245000)

[https://cloud.tencent.com/developer/article/1645897](https://cloud.tencent.com/developer/article/1645897)

[https://zhuanlan.zhihu.com/p/427602828](https://zhuanlan.zhihu.com/p/427602828)

[https://gsmtoday.github.io/2018/03/26/rocketmq-broker/](https://gsmtoday.github.io/2018/03/26/rocketmq-broker/)

[https://kgyhkgyh.gitbooks.io/rocketmq/content/chapter2.html](https://kgyhkgyh.gitbooks.io/rocketmq/content/chapter2.html)

[https://www.modb.pro/db/72476](https://www.modb.pro/db/72476)

---

问题1：超时是如何实现的？

回答1:  NettyRemotingAbstract 中 ,  responseFuture的 responseCommand 初始化的时候就是null， 如果发送成功 但是迟迟没有收到response 就会抛timeout异常。这里的实现是基于countDownLatch. 如果是我的话可能会用个 completableFuture.orTimeout, 或者get(2, TimeUnit.SECONDS)  .示例代码:
[https://github.com/gongchangyou/transactional/blob/master/src/test/java/com/mouse/transactional/FutureTest.java#L46](https://github.com/gongchangyou/transactional/blob/master/src/test/java/com/mouse/transactional/FutureTest.java#L46). 

当然  responseFuture.isSendRequestOK() 这个判断还是需要的，用来区分是否发送成功。

```
RemotingCommand responseCommand = responseFuture.waitResponse(timeoutMillis);
            if (null == responseCommand) { //netty迟迟没有receive 消息，这里就是null
                if (responseFuture.isSendRequestOK()) { //但是request已经发送成功了
                    throw new RemotingTimeoutException(RemotingHelper.parseSocketAddressAddr(addr), timeoutMillis,
                        responseFuture.getCause());
                } else {
                    throw new RemotingSendRequestException(RemotingHelper.parseSocketAddressAddr(addr), responseFuture.getCause());
                }
            }
```

问题2:  多个consumer的offset是如何管理的？
回答2： 参考文章 [https://www.jianshu.com/p/4929947c0b31](https://www.jianshu.com/p/4929947c0b31)  因为broker的代码我还没看过哈，猜测一个可能的处理方式，A,B 两个consumer. nextOffset分别是34，35， 当各自消费完之后，A，B各自去pull 34，35。因为34消息已经被B消费掉了， 所以A可能就pull了个寂寞，pullResult.getPullStatus() 是 NO_NEW_MSG， 此时会将本地 nextOffset置位 35. 

问题3： broker是如何处理某个offset上的消息一直没消费成功的?
回答3:    没有时间看源码，结合一些文章看大概是，consumer本地重试 + sendBack， 将异常的消息重新发送一次，这样offset往前走也没问题。
