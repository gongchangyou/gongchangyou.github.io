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
1.docker run -dit -\-name container_name image_name /bin/bash -c \"/etc/rc.local;/bin/bash\"

进入容器    

1.docker exec -it container_id/container_name /bin/bash

退出 敲exit即可

2.docker attach container_id    
    退出需要ctrl+p+q


