---
layout: post
title: "nacos"
date: 2022-01-13 15:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring Cloud Alibaba - nacos 3 服务注册和订阅


1. 在Application添加 @EnableDiscoveryClient 注解 就能通过 以下调用通
```
            return restTemplate.getForObject("http://service-provider/echo/" + str, String.class);

```

2. 是dubbo RPC调用 TODO

```

```



