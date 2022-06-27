---
layout: post
title: "mongodb"
date: 2022-06-27 10:25:06 +0800
comments: true
category: mysql
tag: [mysql]


---

# MongoDB


代码仓库： 

Java 代码 chunk逻辑

```
public class SplitListToChunks {
 
    public static <T> List<List<T>> split(List<T> list, int size) {
        final AtomicInteger counter = new AtomicInteger();
        return new ArrayList<>(
                list.stream()
                        .collect(Collectors.groupingBy(it -> counter.getAndIncrement() / size))
                        .values());
    }
}
```

batch 搜索
```
@Test
    public void batchGet() {
        val table =db.getCollection("records");
        val ids = new ArrayList<>();
        for (int i = 20140000; i <20147282; i++) {
            ids.add(String.valueOf(i));
        }
        val chunkedList = split(ids, 100);
        val firstStart = System.currentTimeMillis();
        for (val list : chunkedList) {
            val start = System.currentTimeMillis();
            BasicDBObject query = new BasicDBObject("id", new BasicDBObject("$in",list) );
            table.find(query).forEach((Consumer<Document>) document ->{
                log.info("id={}, loc={}",document.get("id"),  document.get("loc"));
            });
            log.info("cost={}", System.currentTimeMillis()- start);
        }
        log.info("allcost={}", System.currentTimeMillis()- firstStart);
    }
```