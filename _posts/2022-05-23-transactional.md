---

layout: post
title: "transactional"
date: 2022-05-23 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# Transactional注解

代码仓库: [https://github.com/gongchangyou/transactional](https://github.com/gongchangyou/transactional)





其实 Mybatis plus中的 ServiceImpl 的save操作都已经是事务级别的了, 我们来试试，如果取消了这个transactional注解会发生什么.

   ![]({{ site.baseurl}}/images/202205/WechatIMG226.png){: width="800" }



比如现在有10个线程，同时更新id=1 的row .value ++ ，结果肯定不是10

```
    private ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10,10,0, TimeUnit.SECONDS, new LinkedBlockingQueue<>(10));

 @Test
    void unThreadSafe() {
        for (int i = 0; i < 10;i++) {
            threadPoolExecutor.submit(() -> {
                val model = testMapper.selectById(1L);
            log.info("start value={}", model.getValue());
            val ret = testMapper.updateById(com.mouse.transactional.repository.db.model.Test
                        .builder()
                        .id(1L)
                        .value(model.getValue() + 1)
                        .build()
                );
                log.info("ret={}", ret);
            });
        }

        try {
            Thread.sleep(2000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```



如何保证呢？ 

1. 我们做个事务方法, 这里使用select for update加行锁, 并添加 @Transactional注解

    ```
    @Slf4j
    @Service
    public class TestService extends ServiceImpl<TestMapper, Test> implements IService<Test> {

        @Transactional
        public void incr(long id) {
            val model = baseMapper.selectOne(new LambdaQueryWrapper<Test>()
                    .eq(com.mouse.transactional.repository.db.model.Test::getId, 1L)
                    .last(" for update"));
            log.info("value={}", model.getValue());
            model.setValue(model.getValue() + 1);
            updateById(model);
        }

    }
    ```

2. 测试一下, 这回成了，test表的value正好加了10.

    ```
         private ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(10,10,0, TimeUnit.SECONDS, new LinkedBlockingQueue<>(10));

        @Test
        void TransactionalUpdate() {
            for (int i = 0; i < 10;i++) {
                threadPoolExecutor.submit(() -> {
                    testService.incr(1L);
                });
            }

            try {
                Thread.sleep(2000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    ```

3. 当然如果抛异常的话，那么里面的操作就不执行. 如下手动抛出异常，里面的更新操作就不会执行。
![]({{ site.baseurl}}/images/202205/WechatIMG227.png){: width="800" }



源码解析:

核心逻辑在 spring-tx包中的 类 TransactionAspectSupport 中：

   ![]({{ site.baseurl}}/images/202205/WechatIMG230.png){: width="800" }