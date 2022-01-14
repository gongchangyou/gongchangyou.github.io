---
layout: post
title: "nacos"
date: 2022-01-13 15:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring Cloud Alibaba - nacos 3 服务注册和订阅


## HTTP调用

1. 在Application添加 @EnableDiscoveryClient 注解 就能通过 以下调用通

```
            return restTemplate.getForObject("http://service-provider/echo/" + str, String.class);

```



## Dubbo服务  
1. dubbo服务 注册  nacos 接口汇总 [https://www.cnblogs.com/lykbk/p/werwerwer35434343434343.html](https://www.cnblogs.com/lykbk/p/werwerwer35434343434343.html)

问题： @DubboService 添加 不管用？

注意到 Nacos-client中 NamingProxy这个类的 callServer方法 line 600，这里会跟nacos服务器交互，包括注册，心跳等
我们来看看为啥注册不成功

正常情况下 应该是能注册成功的

```
curl --location --request POST 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=dubbo-demo-service@@providers:com.braindata.dubbodemo.intf.StuRpcService:1.0.0:dubbo-demo&ip=192.168.1.145&port=20880'
```

但是我的代码偏偏多了几个参数
```
curl --location --request POST 'http://127.0.0.1:8848/nacos/v1/ns/instance?serviceName=dubbo-demo-service@@providers:com.braindata.dubbodemo.intf.StuRpcService:1.0.0:dubbo-demo&ip=192.168.1.145&port=20880&app=unknown&healthy=true&clusterName=null&enable=true&ephemeral=true&groupName=dubbo-demo-service&namespaceId=c7ba173f-29e5-4c58-ae78-b102be11c4f9'
```
尝试下来是 namespace这个参数导致注册失败，具体原因可能需要去看nacos源码了



2. dubbo RPC调用 TODO

   问题1 
	```
	Caused by: java.lang.ClassCastException: java.lang.String cannot be cast to java.lang.Class

	```
   解决： 将spring-conxtext-support 从1.0.11 降到 1.0.10 即可
   https://github.com/apache/dubbo/issues/7274


	问题2 明明服务已经注册到nacos中了，但是
	
	```
	Caused by: java.lang.IllegalStateException: Failed to check the status of the service com.braindata.dubbodemo.intf.StuRpcService. No provider available for the service dubbo-demo/com.braindata.dubbodemo.intf.StuRpcService:1.0.0 from the url nacos://127.0.0.1:8848/org.apache.dubbo.registry.RegistryService?application=dubbo-demo-client&dubbo=2.0.2&group=dubbo-demo&init=false&interface=com.braindata.dubbodemo.intf.StuRpcService&metadata-type=remote&methods=add&pid=67340&register.ip=192.168.1.145&release=2.7.8&revision=0.0.1-SNAPSHOT&side=consumer&sticky=false&timestamp=1642129445698&version=1.0.0 to the consumer 192.168.1.145 use dubbo version 2.7.8
	
	```
```

```



