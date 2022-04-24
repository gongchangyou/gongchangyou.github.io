---
layout: post
title: "user"
date: 2022-04-25 12:25:06 +0800
comments: true
category: python
tag: [python]
---

#  Dolphinscheduler

因为想学习下 如何绑定web用户 和 linux用户



我们可以学习下业内主流任务调度开源系统  [https://github.com/apache/dolphinscheduler](https://github.com/apache/dolphinscheduler)



[https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/start/quick-start.html](https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/start/quick-start.html)



```
docker pull dolphinscheduler.docker.scarf.sh/apache/dolphinscheduler
```





[https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/task/shell.html](https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/task/shell.html)

”worker 执行该任务的时候，会生成一个临时shell脚本， 并使用与租户同名的 linux 用户执行这个脚本。“

"如果Worker所在节点没有这个用户，Worker会在执行任务时创建这个用户。" //这个待确认

按照这个说法，Dolphinscheduler权限很大，都可以创建用户了。



https://dolphinscheduler.apache.org/zh-cn/docs/1.3.1/user_doc/cluster-deployment.html

- 因为是以 sudo -u {linux-user} 切换不同linux用户的方式来实现多租户运行作业，所以部署用户需要有 sudo 权限，而且是免密的。





<font color=red>所以没有黑科技，要么就是有root权限，要么用user 账号密码登录worker服务器</font>