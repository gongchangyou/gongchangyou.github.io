---
layout: post
title: "gitlab-container"
date: 2022-03-09 10:05:06 +0800
comments: true
category: git
tag: [git]
---



#  gitlab container registry

官网说明 ：[https://docs.gitlab.cn/jh/administration/packages/container_registry.html](https://docs.gitlab.cn/jh/administration/packages/container_registry.html


即可以进到docker容器中查看配置文件，也可以先查看挂载数据卷，再看宿主机上的文件。

docker inspect container_name | grep Mounts -A 20

在这个配置中可以看到  容器的镜像仓库的port等配置
```
 cat /srv/gitlab/data/gitlab-rails/etc/gitlab.yml|grep 5005 -C 20
```



每个工程的镜像在如图这个位置查看
![]({{ site.baseurl}}/images/202203/WechatIMG350.png){: width="800" }



不知道如何用docker客户端连接私有镜像库