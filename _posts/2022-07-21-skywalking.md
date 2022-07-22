---
layout: post
title: "skywalking"
date: 2022-07-21 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# skywalking

1.  docker-compose up -d 启动 es + kibana + oap + UI
  [docker-compose.yml]({{ site.baseurl}}/images/202207/docker-compose.yml)  docker-compose up -d

    ```
      version: '2.2'
      services:
        es-node:
         image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
         container_name: es-node
         environment:
           - node.name=es-node
           - cluster.name=es-cluster-7
           - discovery.type=single-node
           - xpack.security.enabled=false
           - "ES_JAVA_OPTS=-Xms1G -Xmx1G"
         ulimits:
           memlock:
             soft: -1
             hard: -1
         volumes:
           - ./data:/usr/share/elasticsearch/data
         ports:
           - 9200:9200
         networks:
           - skywalking-network

        kibana:
         image: docker.elastic.co/kibana/kibana:7.5.0
         environment:
           ELASTICSEARCH_HOSTS: http://es-node:9200
         ports:
           - 5601:5601
         networks:
           - skywalking-network
         depends_on:
           - es-node

        oap:
         image: apache/skywalking-oap-server:8.9.1
         container_name: oap1
         environment:
           SW_STORAGE: elasticsearch
           SW_STORAGE_ES_CLUSTER_NODES: es-node:9200
           TZ: Asia/Shanghai 
         ports:
           - 12800:12800
           - 11800:11800
         networks:
           - skywalking-network
         depends_on:
           - es-node

        ui:
          image: apache/skywalking-ui:8.9.1
          environment:
            SW_OAP_ADDRESS: http://oap1:12800
            TZ: Asia/Shanghai 
          ports:
            - 8088:8080
          networks:
            - skywalking-network
          depends_on:
            - es-node    

      volumes:
       es-data01:
         driver: local

      networks:
       skywalking-network:
         driver: bridge
  
    ```

    发现 es中会创建一堆索引
    ![]({{ site.baseurl}}/images/202207/WechatIMG350.png){: width="800" }



2.  检查以上4个容器是否都正常启动 docker logs XXX ， 打开 localhost:8088 看到 UI启动好了

3.  下载 java agent [https://skywalking.apache.org/downloads/#Agents](https://skywalking.apache.org/downloads/#Agents) 通过设置 -javaagent 配置 SkyWalking Agent  

4.  在idea中设置vm options,   -javaagent:/Users/gongchangyou/learn/skywalking/skywalking-agent/skywalking-agent.jar,  具体的配置可以去 /Users/gongchangyou/learn/skywalking/skywalking-agent/config/agent.config 看看

    ![]({{ site.baseurl}}/images/202207/WechatIMG355.png){: width="800" }

5.  完成 打开 http://localhost:8088/trace 看看 

    ![]({{ site.baseurl}}/images/202207/WechatIMG356.png){: width="800" }

---





问题1: org.apache.skywalking.oap.server.library.module.ProviderNotFoundException

回答1: 应该是ES版本和skywalking版本不匹配导致，把版本先对应好，比如上面的yml中的ES从 8.2.0降到:7.5.0 

skywalking版本保持在 8.9.1






参考文章： [https://skywalking.apache.org/zh/2020-04-19-skywalking-quick-start/](https://skywalking.apache.org/zh/2020-04-19-skywalking-quick-start/)

[https://www.cnblogs.com/xiao987334176/p/13530575.html](https://www.cnblogs.com/xiao987334176/p/13530575.html)

[https://hub.docker.com/r/apache/skywalking-oap-server](https://hub.docker.com/r/apache/skywalking-oap-server)

[https://skywalking.apache.org/docs/main/v8.9.1/en/setup/backend/backend-docker/](https://skywalking.apache.org/docs/main/v8.9.1/en/setup/backend/backend-docker/)