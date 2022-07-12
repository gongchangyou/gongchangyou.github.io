---
layout: post
title: "mongodb index"
date: 2022-07-12 10:25:06 +0800
comments: true
category: mysql
tag: [mysql]


---

# mongodb 索引

通常我们会用 mongodb 来做kv存储。 但是其实也可以用来做关系型数据库，只需要加上索引即可。原理就是 B tree.



参考文章: [https://juejin.cn/post/6844903520747929614](https://juejin.cn/post/6844903520747929614)

[https://haicoder.net/mongodb/mongodb-index-principle.html](https://haicoder.net/mongodb/mongodb-index-principle.html)

[https://blog.csdn.net/guangyacyb/article/details/104339183](https://blog.csdn.net/guangyacyb/article/details/104339183)

[https://mongoing.com/archives/2797](https://mongoing.com/archives/2797)



可以 createIndex单个字段的索引，还能建立联合索引, 1 -1 控制升序和降序

```
 db.person.createIndex( {age: 1, name: -1} )  
```

查看索引

```
db.person.getIndexes() 
```



查看query 是否使用索引,  如果是 [COLLSCAN](https://docs.mongodb.org/manual/reference/explain-results/#queryplanner) 说明全表扫描， 如果是 IXSCAN ，就是使用了索引

```
db.person.find({age:18}).explain()
db.person.find({age:18}).explain("executionStats")
```





