---
layout: post
title: "minio_policy"
date: 2022-04-22 10:25:06 +0800
comments: true
category: go
tag: [go]
---

#  MinIO-policy

权限控制



### 有时候场景是 只针对某个账号有权限read， 先将bucket设置成privacy, 再将这个账号的policy设置成 readonly


policy设置如下

![]({{ site.baseurl}}/images/202204/WechatIMG118.png){: width="800" }



S3 action: [https://iam.cloudonaut.io/reference/s3.html](https://iam.cloudonaut.io/reference/s3.html) 



### 如何获取物理存储地址？ 
不可能！ 因为MinIO是将一个文件拆开存放到多个物理磁盘上，不是直接放到某个磁盘。



### 为什么MinIO可以保证一半节点不可用的时候，服务依然可用？
参考文章：[https://www.cnblogs.com/FLY_DREAM/p/14587022.html](https://www.cnblogs.com/FLY_DREAM/p/14587022.html)

[https://blog.csdn.net/sinat_27186785/article/details/52034588](https://blog.csdn.net/sinat_27186785/article/details/52034588)



纠删码

纠删码是一种恢复丢失和损坏数据的数学算法，目前，纠删码技术在分布式存储系统中的应用主要有三类，阵列纠删码（Array Code: RAID5、RAID6 等）、RS(Reed-Solomon)里德-所罗门类纠删码和 LDPC(LowDensity Parity Check Code)低密度奇偶校验纠删码。Erasure Code 是一种编码技术，它可以将 n 份原始数据，增加 m 份数据，并能通过 n+m 份中的任意 n 份数据，还原为原始数据。即如果有任意小于等于 m 份的数据失效，仍然能通过剩下的数据还原出来

