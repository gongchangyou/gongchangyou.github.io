---
layout: post
title: "spring-cloud-gateway"
date: 2022-02-09 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring-cloud-gateway

## 路由规则如果配置在application.yaml中，那么每次新增路由都需要部署重启gateway，所以这个路由规则必须动态读取，例如存放到nacos中

代码仓库:   [https://github.com/gongchangyou/gateway](https://github.com/gongchangyou/gateway)

http服务 [https://github.com/gongchangyou/dubbo-demo-producer](https://github.com/gongchangyou/dubbo-demo-producer)

RouteDefinitionWriter 负责修改路由， RouteDefinitionLocator 接口只有一个方法 getRouteDefinitions，获取所有路由。

```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-gateway</artifactId>
		</dependency>
```



![code]({{ site.baseurl}}/images/202202/1644386286460.jpg)



重点是 routeDefinitionWriter.save(Mono.just(routeDefinition)).subscribe();

```
//        this.publisher.publishEvent(new RefreshRoutesEvent(this)); 这句好像没啥用
```

