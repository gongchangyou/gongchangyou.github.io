---
layout: post
title: "gitlab-runner"
date: 2022-03-04 10:05:06 +0800
comments: true
category: git
tag: [git]
---



#  gitlab CI/CD

持续集成和部署，其实我更倾向于使用jenkins，因为可以自由编写脚本和手动触发，回滚也比较方便。



gitlab CI/CD就是把部署的脚本 统一到代码仓库。



但是因为现有项目使用 CI/CD 所以还是先熟悉起来。

[官方文档](https://docs.gitlab.com/ee/ci/)





[https://zhuanlan.zhihu.com/p/105157319](https://zhuanlan.zhihu.com/p/105157319)



runner分两种，shared runner / specific runner 看名字就知道了，前者是很多工程共享的runner，后者跟工程是1对1的。



我们先试试看 shared runner. 先打开 http://gitlab.cebsit.ac.cn/admin/runners 

![pgadmin]({{ site.baseurl}}/images/202203/1646634355101.jpg){: width="800" }



注册的时候把url 和token写进去 



官方文档写的很清楚了 [https://docs.gitlab.com/runner/install/docker.html](https://docs.gitlab.com/runner/install/docker.html)

1. docker 启动
  ```
     docker run -d --name gitlab-runner --restart always \
        -v /srv/gitlab-runner/config:/etc/gitlab-runner \
        -v /var/run/docker.sock:/var/run/docker.sock \
        gitlab/gitlab-runner:latest
  ```

2. 注册 runner ，这样gitlab就知道让这台服务器执行job了

   ```
   docker run --rm -it -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register
   ```

问题1： 一直timeout，可能需要显式写tags, 如果你的 .gitlab-ci.yml 中没有显式写tags ,那就把这个选项勾上 "Run untagged jobs"

![pgadmin]({{ site.baseurl}}/images/202203/WechatIMG37.png){: width="800" }

问题2： remote: You are not allowed to download code from this project.

![pgadmin]({{ site.baseurl}}/images/202203/WechatIMG38.jpeg){: width="800" }

尝试去docker容器中新增
```
ssh-keygen -t RSA
```
把 ~/.ssh/id_rsa.pub 添加到工程的用户的ssh key中,  搞定!



注意： gitlab-runner的配置是在 /etc/gitlab-runner/config.toml