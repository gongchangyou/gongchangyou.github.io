---
layout: post
title: "prometheus"
date: 2022-07-07 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# prometheus

参考文章： [https://www.prometheus.wang/quickstart/why-monitor.html](https://www.prometheus.wang/quickstart/why-monitor.html)

[https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)



![]({{ site.baseurl}}/images/202207/architecture.png){: width="800" }

[java client 接入](https://yunlzheng.gitbook.io/prometheus-book/part-ii-prometheus-jin-jie/exporter/custom_exporter_with_java/client_library_java)



跟 [Statsd]({{ site.baseurl}}/2022/05/monitor) 的区别 :

1. statsd是主动udp上报，推的形式. 而prometheus是 提供http接口供 prometheus server pull， 是拉的形式。
2. statsd 协议提供一些常用的metric的类型，通常有timer、counter、gauge和set四种。这样就无需在java client中自行处理



---



## 一个监控mysql的例子： 

参考文章:

[https://yunlzheng.gitbook.io/prometheus-book/part-ii-prometheus-jin-jie/exporter/commonly-eporter-usage/use-promethues-monitor-mysql](https://yunlzheng.gitbook.io/prometheus-book/part-ii-prometheus-jin-jie/exporter/commonly-eporter-usage/use-promethues-monitor-mysql)

[https://hub.docker.com/r/prom/mysqld-exporter](https://hub.docker.com/r/prom/mysqld-exporter)
1. 启动 mysql-exporter 采集指标
    ```
    docker pull prom/mysqld-exporter

    docker run -d -p 9104:9104 --link=my_mysql_container:bdd  \
            -e DATA_SOURCE_NAME="user:password@(bdd:3306)/database" prom/mysqld-exporter
    ```



    访问 http://localhost:9104/  发现已经采集到指标了

2. 启动 Prometheus 将指标保存到 promSQL中

    [配置yml文件]({{ site.baseurl}}/images/202207/prometheus.yml)
    docker 启动 Prometheus ， 注意 -v  前面的路径是宿主机的 ，后面是容器中的。

    ```
    docker run  -d \
  -p 9090:9090 \
  -v /Users/gongchangyou/learn/prometheus:/etc/prometheus/  \
  prom/prometheus
    ```
3. 访问 http://localhost:9090/targets 确认mysql的metris 都收集到了
	![image]({{ site.baseurl}}/images/202207/WechatIMG340.jpeg){: width="800" }
	

4. 在grafana中配置 prometheus数据源, 找个好看的dashboard.

	因为都是docker启动的，这里填写宿主机的ip
	![]({{ site.baseurl}}/images/202207/WechatIMG341.png){: width="800" }
    [import dashboard](https://grafana.com/docs/grafana/v7.5/dashboards/export-import/)



    [https://grafana.com/grafana/dashboards/7362/reviews](https://grafana.com/grafana/dashboards/7362/reviews) download json 或者  copy ID


    [grafana 添加 Prometheus](https://www.cnblogs.com/chanshuyi/p/02_grafana_quick_start.html)

5. 大功告成
	![]({{ site.baseurl}}/images/202207/WechatIMG342.png){: width="800" }