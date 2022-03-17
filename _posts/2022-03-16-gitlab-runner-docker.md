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

一个springboot项目打包时间约4m,因为是在容器内打包， pom里面的依赖还是要重新下载一遍. TODO 看看能否通过数据卷映射的方式把 [https://stackoverflow.com/questions/39004369/how-do-i-mount-a-volume-in-a-docker-container-in-gitlab-ci-yml](https://stackoverflow.com/questions/39004369/how-do-i-mount-a-volume-in-a-docker-container-in-gitlab-ci-yml) 下载好的依赖映射进容器，这样就无需重新下载了,  加速打包过程。

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





### 部署

现在docker镜像已经打包并上传好了。如何配置 .gitlab-ci.yml 启动他呢？

网上很多文章，都是本机启动 docker run,类似如下配置， 这样都是单机的。

```
stages:
  - deploy

docker-deploy:
  stage: deploy  # 执行Job内容
  script:    # 通过Dockerfile生成cicd-demo镜像
    - docker build -t cicd-demo .    # 删除已经在运行的容器
    - if [ $(docker ps -aq --filter name= cicd-demo) ]; then docker rm -f cicd-demo;fi
    # 通过镜像启动容器，并把本机8000端口映射到容器8000端口
    - docker run -d -p 8000:8000 --name cicd-demo cicd-demo
  tags:    # 执行Job的服务器
    - kun
  only:    # 只有在master分支才会执行
    - master
```

我们线上环境肯定是分布式的，那我们如何将gitlab 集成k8s呢？


1. 先在gitlab添加 k8s cluster  ![]({{ site.baseurl}}/images/202203/WechatIMG57.png){: width="800" }

2. 用k8s 设置deployment  neuroviz-server-java  TODO 这个deployment.yaml不好写啊

   ```
   1. kubectl create deployment neuroviz-server-java 
   2. kubectl expose deployment neuroviz-server-java --type=NodePort --port=8090 --target-port=8080
   3. kubectl get services neuroviz-server-java
   
   
   ```

   

3. 把.gitlab-ci.yml 加上如下配置,  重点是 set image 命令 [http://docs.kubernetes.org.cn/670.html#i-2](http://docs.kubernetes.org.cn/670.html#i-2)

```
deploy:
  only:
    - master
  stage: deploy
  image:     
    name: bitnami/kubectl:latest
    entrypoint: [""]
  script:
    - export HOME=/tmp
    - kubectl config set-cluster k8s --server=$K8S_SERVER --certificate-authority=$K8S_CERT --embed-certs=true
    - kubectl config set-credentials k8s-gitlab --token=$K8S_TOKEN
    - kubectl config set-context k8s --cluster=k8s --user=k8s-gitlab
    - kubectl config use-context k8s
    - kubectl set image deployment/neuroviz-server-java master=$CI_REGISTRY_IMAGE/$CI_COMMIT_REF_NAME:$CI_COMMIT_SHORT_SHA

```



