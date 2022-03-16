---
layout: post
title: "gitlab-runner-docker"
date: 2022-03-16 10:05:06 +0800
comments: true
category: git
tag: [git]
---



#  gitlab runner docker

### docker容器集成

[https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#register-docker-runner](https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#register-docker-runner)

一般来说除了shell，我们还希望集成docker容器，在docker中编译打包，并且打包成docker镜像，供k8s部署。



1. 注册一个新的runner，executor是docker的那种，不是上期说的shell
2. 编辑你的.gitlab-ci.yml， 把image添加进去。





因为我们的gitlab-runner是docker启动的，所以先进入容器

```
docker exec -it gitlab-runner bash
```

敲下面的命令

```
   gitlab-runner register \
     --url "https://gitlab.example.com/" \
     --registration-token "PROJECT_REGISTRATION_TOKEN" \
     --description "docker 各种描述" \
     --executor "docker" \
     --template-config /tmp/test-config.template.toml \ ##这行先不需要，这个是模板，每个job都会去pull里面的镜像
     --docker-image docker:latest
```


然后跟第一期一样，去gitlab的配置界面把工程的runner设置成这个





修改.gitlab-ci.yml, 下面的这个image:maven:3-jdk-11 会保留在runner中，等下次再打包的时候就不会重新pull了

一个springboot项目打包时间约4m.

```
stages:
  - build
  - publish
  - deploy

#打包
build-job:
  stage: build
  image: maven:3-jdk-11
#  script: "mvn package -B"
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"
    - mvn package -B
  artifacts:
    paths:
      - target/*.jar
```





### 上传docker镜像

