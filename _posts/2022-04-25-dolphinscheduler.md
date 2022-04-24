---
layout: post
title: "dolphinscheduler"
date: 2022-04-25 10:25:06 +0800
comments: true
category: java
tag: [java]
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

"如果Worker所在节点没有这个用户，Worker会在执行任务时创建这个用户。"

按照这个说法，Dolphinscheduler权限很大，都可以创建用户了。



https://dolphinscheduler.apache.org/zh-cn/docs/1.3.1/user_doc/cluster-deployment.html

- 因为是以 sudo -u {linux-user} 切换不同linux用户的方式来实现多租户运行作业，所以部署用户需要有 sudo 权限，而且是免密的。





<font color=red>所以没有黑科技，要么就是有root权限，要么用user 账号密码登录worker服务器</font>



在这个循环中，从queue里不停的获取task， 并交给taskExecuteThread去run

![]({{ site.baseurl}}/images/202204/WechatIMG120.png){: width="800" }

![]({{ site.baseurl}}/images/202204/WechatIMG119.png){: width="800" }

```
TaskExecuteThread.run
TaskExecuteThread.task.handle()
ShellTask.handle()
AbstractCommandExecutor.run
AbstractCommandExecutor.buildProcess 在这里面会切换用户执行cmd sudo -s username cmd
```



FAQ :  [https://github.com/apache/dolphinscheduler/blob/dev/docs/docs/zh/faq.md](https://github.com/apache/dolphinscheduler/blob/dev/docs/docs/zh/faq.md)