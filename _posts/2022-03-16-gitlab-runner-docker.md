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


然后跟第一期一样，去gitlab的配置界面把工程的runner设置成上述runner.



### 打包

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



在.gitlab-ci.yml中添加如下配置

```
publish:
  stage: publish
  only:
    - master
  image: docker:19.03.14
  services:
    - name: docker:19.03.14-dind
      alias: docker
      command: ["--insecure-registry=172.16.102.20:5005"]
  script:
    - docker build -t $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_NAME/preprocess:$CI_COMMIT_SHORT_SHA -f Dockerfile .
    - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN $CI_REGISTRY
    - docker push $CI_REGISTRY_IMAGE/$CI_COMMIT_REF_NAME/preprocess:$CI_COMMIT_SHORT_SHA

```

问题1：  msg="failed to dial gRPC: cannot connect to the Docker daemon. Is 'docker daemon' running on this host?: dial tcp: lookup docker on 10.10.10.12:53: no such host"

![]({{ site.baseurl}}/images/202203/WechatIMG54.png){: width="800" }

往上翻到一个warning，应该是dind service没成功启动

![]({{ site.baseurl}}/images/202203/WechatIMG55.png){: width="800" }

解决1：[https://gitlab.com/gitlab-org/gitlab-runner/-/issues/1544](https://gitlab.com/gitlab-org/gitlab-runner/-/issues/1544) 这篇文章说 要加个参数 “privileged = true” 改了之后重启gitlab-runner


问题2： Cannot connect to the Docker daemon at tcp://docker:2375. Is the docker daemon running?

![]({{ site.baseurl}}/images/202203/WechatIMG56.png){: width="800" }

解决2： 害得是业界良心啊 [https://stackoverflow.com/questions/61105333/cannot-connect-to-the-docker-daemon-at-tcp-localhost2375-is-the-docker-daem](https://stackoverflow.com/questions/61105333/cannot-connect-to-the-docker-daemon-at-tcp-localhost2375-is-the-docker-daem)

```
 services:
    - name: docker:19.03.14-dind
      alias: docker-dind #这个别名下面有用
      command: ["--insecure-registry=172.16.102.20:5005"]
  variables:
    # Tell docker CLI how to talk to Docker daemon; see
    # https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#use-docker-in-docker-executor
    DOCKER_HOST: tcp://docker-dind:2375/
    # Use the overlayfs driver for improved performance:
    DOCKER_DRIVER: overlay2
    DOCKER_TLS_CERTDIR: ""
```