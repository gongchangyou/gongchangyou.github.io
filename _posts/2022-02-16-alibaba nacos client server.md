---
layout: post
title: "nacos serverlist"
date: 2022-02-16 10:05:06 +0800
comments: true
category: java
tag: [java]
---



#  nacos serverList & endpoint

## 两种设置nacos 服务地址的方式
	1. 生产环境的话，我们一般使用endpoint 固定域名
	2. 测试环境我们可以指定server-addr
	如下所示

![config]({{ site.baseurl}}/images/202202/1644977241350.jpg){: width="1000" }
	
![set server list]({{ site.baseurl}}/images/202202/1644977146089.jpg){: width="1000" }
	
	优先选择server list, 如果没有配置，则请求http://域名/nacos/serverlist 接口获取 nacos注册服务器列表

![set server list code]({{ site.baseurl}}/images/202202/1644977321417.jpg){: width="1000" }

