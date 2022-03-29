---
layout: post
title: "docker常用命令"
date: 2017-11-28 10:25:06 +0800
comments: true
category: shell
tag: [shell]
---

生成镜像    
docker build -f Dockerfile -t panshi .

批量删除image   
docker images -a |tr -s ' '|cut -d ' ' -f3|xargs docker rmi


批量删除容器  
docker ps -a -q |xargs docker rm

启动容器    
1.docker run -dit -\-name container_name -v /tmp/outer:/tmp/inner image_name /bin/bash -c \"/etc/rc.local;/bin/bash\"

进入容器    

1.docker exec -it container_id/container_name /bin/bash

退出 敲exit即可

2.docker attach container_id    
    退出需要ctrl+p+q



跟docker容器一起启动

--restart=always



存储/载入

```
docker save -o 要保存的文件名  要保存的镜像


docker load --input 文件

docker load < 文件名
```