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




