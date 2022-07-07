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



批量编辑 关键字 $set

```
Bson f = Filters.and(Filters.eq("id", 2),Filters.eq("name", "Eric2"));
        //成功只更新name字段
        db.getCollection("user").updateMany(f, new Document("$set",
                new HashMap<>(){{
                    put("name", "Eric20");
                }}
        ));
```



批量追加 关键字 $addToSet

```
Bson f = Filters.and(Filters.eq("id", 3),Filters.eq("name", "Eric3"));
        db.getCollection("user").updateMany(f, new Document("$addToSet",
                new HashMap<>(){{
                    put("nickname", "d3");
                }}
        ));
```

批量追加  合并数组. 关键字 $each

```
//$each 关键字 在array类型的field中追加 多个element
        db.getCollection("user").updateMany(f, new Document("$addToSet",
                new HashMap<>() {{
                    put("nickname", new HashMap<>(){{
                        put("$each", new ArrayList<>(){{
                            add("e4");
                            add("e5");

                        }});
                    }});
                }}
        ));
```



单独修改数组中的某个值 .$

```
//.$ 的意思就是数组的下标
        Bson f5 = Filters.and(Filters.eq("id", 5), Filters.eq("nickname", "c5"));
        db.getCollection("user").updateMany(f5, new Document("$set", new HashMap<>(){{
            put("nickname.$", "f5");
        }} ));
```



参考文章： [https://www.mongodb.com/docs/manual/reference/operator/update/addToSet/](https://www.mongodb.com/docs/manual/reference/operator/update/addToSet/)

[https://www.modb.pro/db/73050](https://www.modb.pro/db/73050)