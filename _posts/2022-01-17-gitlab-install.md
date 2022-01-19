---
layout: post
title: "gitlab-install"
date: 2022-01-17 13:25:06 +0800
comments: true
category: java
tag: [java]
---



#  gitlab-install

[https://blog.csdn.net/qq_35844177/article/details/106876923](https://blog.csdn.net/qq_35844177/article/details/106876923)

1. sudo apt update
sudo apt install ca-certificates curl openssh-server postfix


2. cd /tmp
    curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh

    less /tmp/script.deb.sh

    sudo bash /tmp/script.deb.sh

    sudo apt install gitlab-ce

如果一切顺利 那么 gitlab-ctl start 就够了。




# 可能你的服务器上已经安装了其他的应用 占用了原有的端口
则需要去修改配置文件

vim /etc/gitlab/gitlab.rb
```
external_url 'http://10.1.48.192:9091' 

 puma['port'] =9092
```

## 重置配置 并 重启

gitlab-ctl reconfigure

gitlab-ctl restart


## 查看日志命令：

gitlab-ctl tail 

gitlab-ctl tail |grep -i error

## 查看端口命令：

lsof -i:9091







# 推荐 docker 安装

```
docker pull gitlab/gitlab-ce:[version]  最好选择跟主一个版本，避免出现什么幺蛾子

docker run -detach --publish 8443:443 --publish 9091:80 --publish 8022:22  --publish 5005:5005 --name gitlab --restart always --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:13.7.1-ce.0


可能需要改一些配置

vi /srv/gitlab/config/gitlab.rb

external_url 


postgresql['shared_buffers'] = "256MB"

如果puma 启动不了 看到pid不停变大
gitlab-ctl tail puma

puma['port'] = 8888
gitlab_workhorse['auth_backend'] = "http://localhost:8888"



```