---

layout: post
title: "disruptor"
date: 2022-05-18 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# Disruptor

Disruptor是英国外汇交易公司LMAX开发的一个高性能队列. 多消费者之间还能形成依赖(then关键字)



代码仓库: [https://github.com/gongchangyou/disruptor](https://github.com/gongchangyou/disruptor)

1. Pom.xml

   ```
         <!-- https://mvnrepository.com/artifact/com.lmax/disruptor -->
           <dependency>
               <groupId>com.lmax</groupId>
               <artifactId>disruptor</artifactId>
               <version>3.4.4</version>
           </dependency>
   ```

2. 单生产者，单消费者
	```
   /**
        * 单生产者 单消费者
        */
       @Test
       void contextLoads() {
           // 队列中的元素
           class Element {
   
               private int value;
   
               public int get(){
                   return value;
               }
   
               public void set(int value){
                   this.value= value;
               }
   
           }
   
           // 生产者的线程工厂
           ThreadFactory threadFactory = new ThreadFactory(){
               int i = 0;
               @Override
               public Thread newThread(Runnable r) {
                   return new Thread(r, "simpleThread " + i++);
               }
           };
   
           // RingBuffer生产工厂,初始化RingBuffer的时候使用
           EventFactory<Element> factory = new EventFactory<Element>() {
               @Override
               public Element newInstance() {
                   return new Element();
               }
           };
   
           // 处理Event的handler
           EventHandler<Element> handler = new EventHandler<Element>(){
               @Override
               public void onEvent(Element element, long sequence, boolean endOfBatch)
               {
                   log.info("element={}, sequence={}, endOfBatch={}", element.get(), sequence, endOfBatch);
   //                System.out.println("Element: " + element.get());
               }
           };
   
           // 阻塞策略
           BlockingWaitStrategy strategy = new BlockingWaitStrategy();
   
           // 指定RingBuffer的大小
           int bufferSize = 16;
   
           // 创建disruptor，采用单生产者模式
           Disruptor<Element> disruptor = new Disruptor(factory, bufferSize, threadFactory, ProducerType.SINGLE, strategy);
   
           // 设置EventHandler
           disruptor.handleEventsWith(handler);
   
           // 启动disruptor的线程
           disruptor.start();
   
           RingBuffer<Element> ringBuffer = disruptor.getRingBuffer();
   
           for (int l = 0; true; l++) {
               // 获取下一个可用位置的下标
               long sequence = ringBuffer.next();
               try {
                   // 返回可用位置的元素
                   Element event = ringBuffer.get(sequence);
                   // 设置该位置元素的值
                   event.set(l);
               } finally {
                   ringBuffer.publish(sequence);
               }
               try {
                   Thread.sleep(10);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       }
  ```

问题1： 有不同的消费者依赖怎么办？
回答1：  关键字 then

```
	//多个消费者间形成依赖关系，每个依赖节点的消费者为单线程。
        disruptor.handleEventsWith(new OrderHandler1("1")).then(new OrderHandler1("2"), new OrderHandler1("3")).then(new OrderHandler1("4"));
       
```

问题2： 希望不同的消费者不重复的消费怎么办？即多个consumer 加速消费
回答2:  handleEventsWithWorkerPool

```
disruptor.handleEventsWithWorkerPool(new OrderHandler1("1"), new OrderHandler1("2"));
```

问题3： 希望不同的消费者独立消费
回答3： handleEventsWith 多个handler即可
```
 disruptor.handleEventsWith(new OrderHandler1("1"), new OrderHandler1("2"));
```



问题4： 多生产者情况下，如果其中一个生产者的生产速度太低， 游标迟迟无法往下移，消费者会如何表现？会阻塞吗？

回答4： 一般不会出现这种情况，sequence是先取再发布的，如果先取sequence之后， 又做了一些耗时的操作才publish， 则会阻塞消费者。所以写代码的时候尽量把取sequence和publish 靠近.





官方文档: [https://lmax-exchange.github.io/disruptor/disruptor.html](https://lmax-exchange.github.io/disruptor/disruptor.html)

参考文章: [https://tech.meituan.com/2016/11/18/disruptor.html](https://tech.meituan.com/2016/11/18/disruptor.html)

[https://www.cnblogs.com/pku-liuqiang/p/8544700.html](https://www.cnblogs.com/pku-liuqiang/p/8544700.html)

[https://www.cnblogs.com/pku-liuqiang/p/8532737.html](https://www.cnblogs.com/pku-liuqiang/p/8532737.html)

[https://cloud.tencent.com/developer/article/1701690](https://cloud.tencent.com/developer/article/1701690)



### 生产者调用逻辑

![]({{ site.baseurl}}/images/202205/WechatIMG279.png){: width="800" }

其中第3步做两件事

1.  
    1.  情况1： 设置游标(单个生产者的情况)
    2.  情况2：计算availableBuffer中可用的位子 （多个生产者的情况）. 因为生产者获取到sequence和 这个sequence可用是两码事，所以需要额外的availableBuffer来保存哪些sequence是可用的。[https://blog.csdn.net/hilaryfrank/article/details/105484157](https://blog.csdn.net/hilaryfrank/article/details/105484157)
2.  根据等待策略通知消费者



### 多消费者的情况

每个消费者对应一个线程 BatchEventProcessor，各自维护游标 sequence

<font color="red">本质上就是利用cpu性能 来加速</font>

>   # **等待策略**
>
>   源码地址：https://github.com/LMAX-Exchange/disruptor/blob/master/src/main/java/com/lmax/disruptor/WaitStrategy.java
>
>   **「BlockingWaitStrategy」**
>
>   Disruptor的默认策略是BlockingWaitStrategy。在BlockingWaitStrategy内部是使用锁和condition来控制线程的唤醒。BlockingWaitStrategy是最低效的策略，但其对CPU的消耗最小并且在各种不同部署环境中能提供更加一致的性能表现。
>
>   **「SleepingWaitStrategy」**
>
>   SleepingWaitStrategy 的性能表现跟 BlockingWaitStrategy 差不多，对 CPU 的消耗也类似，但其对生产者线程的影响最小，通过使用`LockSupport.parkNanos(1)`来实现循环等待。
>
>   **「YieldingWaitStrategy」**
>
>   YieldingWaitStrategy是可以使用在低延迟系统的策略之一。YieldingWaitStrategy将自旋以等待序列增加到适当的值。在循环体内，将调用`Thread.yield()`以允许其他排队的线程运行。在要求极高性能且事件处理线数小于 CPU 逻辑核心数的场景中，推荐使用此策略；例如，CPU开启超线程的特性。
>
>   **「BusySpinWaitStrategy」**
>
>   性能最好，适合用于低延迟的系统。在要求极高性能且事件处理线程数小于CPU逻辑核心数的场景中，推荐使用此策略；例如，CPU开启超线程的特性。
>
>   **「PhasedBackoffWaitStrategy」**
>
>   自旋 + yield + 自定义策略，CPU资源紧缺，吞吐量和延迟并不重要的场景。

源码解析： TODO

