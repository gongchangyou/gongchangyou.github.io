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



