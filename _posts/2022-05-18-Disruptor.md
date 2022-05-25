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



官方文档: [https://lmax-exchange.github.io/disruptor/disruptor.html](https://lmax-exchange.github.io/disruptor/disruptor.html)

参考文章: [https://tech.meituan.com/2016/11/18/disruptor.html](https://tech.meituan.com/2016/11/18/disruptor.html)

[https://www.cnblogs.com/pku-liuqiang/p/8544700.html](https://www.cnblogs.com/pku-liuqiang/p/8544700.html)

[https://www.cnblogs.com/pku-liuqiang/p/8532737.html](https://www.cnblogs.com/pku-liuqiang/p/8532737.html)

[https://cloud.tencent.com/developer/article/1701690](https://cloud.tencent.com/developer/article/1701690)



源码解析： TODO

