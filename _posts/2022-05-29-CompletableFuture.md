---

layout: post
title: "CompletableFuture"
date: 2022-05-29 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# CompletableFuture

参考文章: [https://juejin.cn/post/6844903594165026829](https://juejin.cn/post/6844903594165026829)

[https://colobu.com/2016/02/29/Java-CompletableFuture/](https://colobu.com/2016/02/29/Java-CompletableFuture/)



一个手动complete的例子

```
    @Test
    void complete() {
        CompletableFuture cf = new CompletableFuture<String>();

        val cf1 = cf.thenApply((Object str) -> {
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return ((String)str).replace("a", "d");
        });

        cf.complete("aad"); //这里会触发cf后面的thenApply方法，上述的sleep会同步阻塞。

        try {
            val value = cf.get();
            val value1 = cf1.get(Integer.MAX_VALUE, TimeUnit.MILLISECONDS);
            log.info("value={} value1={}",value,value1);
        } catch (InterruptedException | ExecutionException | TimeoutException e) {
            e.printStackTrace();
        }
    }
```

