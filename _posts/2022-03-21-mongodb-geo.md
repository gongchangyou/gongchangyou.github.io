---
layout: post
title: "mongodb"
date: 2022-03-21 11:05:06 +0800
comments: true
category: mysql
tag: [mysql]
---

#  MongoDB geo搜索测评

代码仓库：[https://github.com/gongchangyou/mongodb-test](https://github.com/gongchangyou/mongodb-test)



docker 安装MongoDB [https://www.runoob.com/docker/docker-install-mongodb.html](https://www.runoob.com/docker/docker-install-mongodb.html)

mongodb命令

```
如果刚刚创建了用户，进入db操作CRUD的之前记得认证
db.auth('admin','123456')

#查看所有db
show dbs

#写入
db.records.insertOne( {
  id:1,
  loc :  [ 55.5, 42.3 ]
} )


#find all
db.records.find( {} )

#创建地理索引
db.records.createIndex( { "loc": "2d" } )

```



box搜索 [https://mongodb-documentation.readthedocs.io/en/latest/reference/operator/within.html#gsc.tab=0](https://mongodb-documentation.readthedocs.io/en/latest/reference/operator/within.html#gsc.tab=0)

```
db.records.find( {
   loc: { $geoWithin: { $box:  [ [ 0, 0 ], [ 100, 100 ] ] } }
} )
```

可惜没办法搜索热力图，即多个box 一起查询。1亿数据查询速度约20ms，单个box还行。



注意： 默认的范围是 [-180,180] ，如果需要修改起止点范围

```
删除索引
dropIndex(”loc_2d")
重建索引
reIndex()

创建索引
db.records.createIndex( { "loc": "2d" } , { min : 0, max : 100000 } )
```

