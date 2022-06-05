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

