---
layout: post
title: "kryo"
date: 2022-02-15 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  dubbo 序列化配置 Kryo



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





原理：

1. 我们配置的序列化框架 比如 kryo , 这个字符串"kryo"是设置在了ProtocolConfig中

2. 真正需要序列化的时候，是由AbstractCodec 根据 ExtensionLoader.getExtension 获取 

3. ExtensionLoader 中有个属性叫 cachedClasses,  是个map，会创建type名到类的映射

    ```
    private final Holder<Map<String, Class<?>>> cachedClasses = new Holder<>();
    ```

4. 那么 cachedClasses 是何时 初始化的呢？这里涉及到 SPI .  ExtensionLoader 会去扫描 文件夹 META-INF/dubbo/internal下的所有 Serialization的实现类。 即文件名是 org.apache.dubbo.common.serialize.Serialization 的 文件。

   ```
   #文件 org.apache.dubbo.common.serialize.Serialization 的内容 kryo就是name
   kryo=org.apache.dubbo.common.serialize.kryo.KryoSerialization
kryo2=org.apache.dubbo.common.serialize.kryo.optimized.KryoSerialization2
   ```

    ```
    private Map<String, Class<?>> loadExtensionClasses() {
            cacheDefaultExtensionName();

            Map<String, Class<?>> extensionClasses = new HashMap<>();

            for (LoadingStrategy strategy : strategies) {
                loadDirectory(extensionClasses, strategy.directory(), type.getName(), strategy.preferExtensionClassLoader(), strategy.overridden(), strategy.excludedPackages());
                loadDirectory(extensionClasses, strategy.directory(), type.getName().replace("org.apache", "com.alibaba"), strategy.preferExtensionClassLoader(), strategy.overridden(), strategy.excludedPackages());
            }

            return extensionClasses;
        }
    ```

5. 最终在 saveInExtensionClass 方法中 把 class 和name的映射设置好

![set class]({{ site.baseurl}}/images/202202/1644917724845.jpg){: width="1000" }




问题：

本来想看看默认序列化是如何设置的，有两种方法

法1，在SPI注解上加属性 ，由属性控制

![1]({{ site.baseurl}}/images/202202/1645257330444.jpg){: width="1000" }

法2，设置一个Constant类, 将默认值填进去

![1]({{ site.baseurl}}/images/202202/1645257359049.jpg){: width="1000" }



现在就是初始化的时候是根据SPI的value，但是获取实现类的时候又用到了Constant类，让人有点疑惑，两个默认值

![1]({{ site.baseurl}}/images/202202/1645257426431.jpg){: width="1000" }

![1]({{ site.baseurl}}/images/202202/WechatIMG587.png){: width="1000" }
