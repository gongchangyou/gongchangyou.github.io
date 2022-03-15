---
layout: post
title: "es-heatmap"
date: 2022-03-15 11:05:06 +0800
comments: true
category: mysql
tag: [mysql]
---

#  ElasticSearch 热力图性能测评

代码仓库：[https://github.com/gongchangyou/elasticsearch-test](https://github.com/gongchangyou/elasticsearch-test)

上述仓库里面也有建立索引+bulk批量创建数据的测试代码 ,1亿数据一会儿就能创建好



java客户端 [https://www.elastic.co/guide/en/elasticsearch/client/java-api-client/current/introduction.html](https://www.elastic.co/guide/en/elasticsearch/client/java-api-client/current/introduction.html)

创建索引: [https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-create-index.html#_index_settings](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-create-index.html#_index_settings)


测试服务器8核,  线上环境会更好

10000个点热力图40ms

1000000 个点 热力图, 不测了。直接上7kw个点

7kw 个点 热力图

 搜索10*10的矩形， 首次查询5-6s，效果还是非常不错的。

 搜索100*100的矩形， 首次查询10-11s. 

搜索1000*1000， 报错 "too_many_buckets_exception" 这是ES为了性能考虑。 我们尝试调大一点 2000000。实测23s左右耗时。

![]({{ site.baseurl}}/images/202203/WechatIMG51.png){: width="800" }

当然实际情况更多的是查看局部，这样就会退化成500*500个bucket，这样查询耗时依然是10s左右，可以接受.

```
PUT /_cluster/settings
{"persistent": {"search.max_buckets": 2000000}}
```



现在唯一的问题就是存储空间，7kw个点的是4g+ ，如果200个数据集将近1T。

![]({{ site.baseurl}}/images/202203/WechatIMG50.png){: width="800" }




问题1： JacksonJsonpMapper NoClassDefFoundError 

```
   <properties>
        <jakarta-json.version>2.0.1</jakarta-json.version> #这里把版本号设置成2.0.1
        <java.version>11</java.version>
    </properties>
```





问题2： Missing [X-Elastic-Product] header. 

解决2： elasticsearch 升级下版本，7.17.0+





问题3：  返回值大于100M 

```
java.io.IOException: entity content is too long [107665275] for the configured buffer limit [104857600]

```

解决3:  别人的解决方案 [https://www.cnblogs.com/wwjj4811/p/15261125.html](https://www.cnblogs.com/wwjj4811/p/15261125.html)

解决3：上述解决方案太复杂了，我觉得ES设置成100M上限还是挺有道理的，先看看我们本身的query，咱们这个步长因为精度问题可能在小数点后很多位，超出我们的需要，本身0.0004就够了，结果 0.00040000002，然后AggregationRange需要将float转换成String，所以这里字符串长度就长了许多。至此，我们就直接把小数点位数控制好即可

```
 /**
     * 保留小数点后4位
     */
    private String decimal(float x){
        return String.format("%.4f", x);
    }
```



![]({{ site.baseurl}}/images/202203/WechatIMG52.png){: width="800" }