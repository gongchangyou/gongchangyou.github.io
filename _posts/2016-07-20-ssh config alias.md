---
layout: post
title: "ssh rsa"
date: 2016-07-20 13:24:06 +0800
comments: true
category: shell
tag: [shell]
---

1.ssh-keygen  -t rsa

2.在 ~/.ssh/config中添加

		Host lfs-cn-manage 
		  HostName localhost  #这里写ip
		  User aiming
		  IdentityFile ~/.ssh/id_rsa.aiming

3.chmod 666 ~/.ssh/config #勿忘

4.将pub key 添加到服务端的 ~/.ssh/authorized_keys 中

搞定！

