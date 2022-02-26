---
layout: post
title: "gitlab-sync"
date: 2022-01-18 13:25:06 +0800
comments: true
category: java
tag: [java]
---



#  gitlab-主从同步

[https://www.jianshu.com/p/52de6a8d29d6](https://www.jianshu.com/p/52de6a8d29d6)

### lsyncd 安装在主服务器上



lsyncd 配置完事就能跑了



配置

```
cat /etc/lsyncd.conf 
```

日志
```
tail -f /var/log/lsyncd/lsyncd.log
```



问题： 创建了一个新的project， 从库没显示

我显式重启了docker容器才在从库看到

```
docker run -detach --publish 8443:443 --publish 9091:80 --publish 8022:22  --publish 5005:5005 --name gitlab --restart always --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:13.7.1-ce.0
```



如果报一些奇怪的异常，比如连不上 postgresql 5432之类的， Don't panic ! 很可能是主从同步不一致导致的文件错误。 去主库上 重启下 systemctl restart lsyncd。等两边数据一致了再重启下,docker stop ,docker start.