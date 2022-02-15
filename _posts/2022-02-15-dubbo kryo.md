---
layout: post
title: "kryo"
date: 2022-02-15 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Kryo



注意版本要跟  dubbo-spring-boot-starter 相同,2.7.8

```
<!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-serialization-kryo -->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-serialization-kryo</artifactId>
    <version>2.7.8</version>
</dependency>

```



在producer 和 consumer加一句配置即可

```
dubbo.protocol.serialization=kryo
```

