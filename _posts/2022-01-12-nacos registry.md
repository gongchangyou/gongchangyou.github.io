---
layout: post
title: "nacos 2 应用接入"
date: 2022-01-12 15:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring Cloud Alibaba - nacos 2 应用接入
两个方法

1. Nacos官方用nacos client + spring boot 模式
2. Spring Cloud Alibaba 提供的 Discovery 模式

注意：如果关闭应用的话，nacos中的配置还在





application.properties没有拷贝到target文件夹的原因？

```
<packaging>pom</packaging>
```
改成 下面，或者直接这行删掉
```
<packaging>jar</packaging>
```

https://blog.csdn.net/qq_37651267/article/details/99305176



