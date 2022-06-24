---
layout: post
title: "es cluster"
date: 2022-06-24 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# ES 集群

java访问ES 集群，两种方法：

1.  推荐 走内网虚拟ip(VIP) 参考文章： [https://cloud.tencent.com/document/product/845/19538](https://cloud.tencent.com/document/product/845/19538)

    1.  配置虚拟ip的命令:

        ```
        ifconfig eth0:1 192.168.0.107 netmask 255.255.0.0
        
        #下线虚拟ip
        ifconfig eth0:1 down
        ```

        

2.  把cluster node都添加到client中,类似下面的代码

    ```
    RestHighLevelClient client = new RestHighLevelClient(
                     RestClient.builder(
                     		//Es连接信息，我用的是伪集群哈哈哈
                             new HttpHost("localhost", 9200, "http"),
                             new HttpHost("localhost", 9201, "http"),
                             new HttpHost("localhost", 9202, "http")
                     )
             );
    ```

    