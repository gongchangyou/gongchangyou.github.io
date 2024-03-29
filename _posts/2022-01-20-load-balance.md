---
layout: post
title: "load balance"
date: 2022-01-20 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring Cloud Alibaba - nacos 4 负载均衡

1. 创建两个不同端口的 producer 

    ```
       	package com.braindata.dubbodemo.config;
       
        import java.util.Map;
        import java.util.Map.Entry;
        import javax.annotation.PostConstruct;

        import org.apache.dubbo.common.utils.NetUtils;
        import org.apache.dubbo.config.ProtocolConfig;
        import org.springframework.beans.BeansException;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.context.ApplicationContext;
        import org.springframework.context.ApplicationContextAware;
        import org.springframework.context.ConfigurableApplicationContext;
        import org.springframework.stereotype.Component;

        /**
         * 一个机器部署两个dubbo生产者会产生端口占用问题，为了解决这个问题，在加载dubbo配置文件之前，先设置没被占用的端口
         * @author cookie
         */
        @Component
        public class DynamicDubboPortReader implements ApplicationContextAware{
            @Autowired
            private ApplicationContext applicationContext;

            private int port = 20880;

            @PostConstruct
            public void init(){
                Map<String, ProtocolConfig> map = applicationContext.getBeansOfType(ProtocolConfig.class);
                for(Entry<String,ProtocolConfig> con: map.entrySet()){
                    port = NetUtils.getAvailablePort();
                    System.out.println("=========="+port);
                    con.getValue().setPort(port);
                }
            }

            @Override
            public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
                this.applicationContext = (ConfigurableApplicationContext)applicationContext;
            }

        }

   ```
   
2. 如果producer 还有 server.port 可以通过设置启动项 -Dserver.port=7824 来改变
3. 启动consumer ，测试发现 DubboReference 注解已经集成负载均衡功能了， 如果想指定某一个producer，可以通过 sticky=true 来设置。

那dubbo是如何实现负载均衡的呢？ 我们来看下源代码 [https://github.com/apache/dubbo](https://github.com/apache/dubbo)



[https://blog.csdn.net/caoyuanyenang/article/details/114837786](https://blog.csdn.net/caoyuanyenang/article/details/114837786) 这篇文章说的很详尽了



我使用dubbo 2.7.8

其中 AbstractClusterInvoker 

line 257 会根据服务名去注册中心获取ip

Line 258 获取loadbalance

![lb]({{ site.baseurl}}/images/202201/1642651014052.jpg){: width="1000" }





