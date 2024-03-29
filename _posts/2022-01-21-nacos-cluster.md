---
layout: post
title: "cluster"
date: 2022-01-20 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring Cloud Alibaba - nacos 5 集群部署

[https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html](https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html)

1. 域名 + SLB模式(内网SLB，不可暴露到公网，以免带来安全风险)

![embbed]({{ site.baseurl}}/images/202201/deployDnsVipMode.jpeg)

配置 cluster.conf
nacos应该会将注册服务同步给其他ip
    

    ```
    #it is ip
    #example
    192.168.1.145:8848
    192.168.1.146:8848
    
    ```


2. 线上环境使用 外置数据源 [https://github.com/alibaba/nacos/blob/master/distribution/conf/nacos-mysql.sql](https://github.com/alibaba/nacos/blob/master/distribution/conf/nacos-mysql.sql)

3. 修改 db的url和帐号密码  并打jar包 [https://github.com/alibaba/nacos/blob/master/distribution/conf/application.properties](https://github.com/alibaba/nacos/blob/master/distribution/conf/application.properties)

4. 重启nacos 

```
sh startup.sh
```

## 又： 如果 第2步选择了内置数据源
```
sh startup.sh -p embedded
```


