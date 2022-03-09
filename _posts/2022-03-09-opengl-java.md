---
layout: post
title: "java-opengl"
date: 2022-03-09 10:05:06 +0800
comments: true
category: java
tag: [java]
---



#  java opengl

Lwjgl  vs jogl ?



我看https://mvnrepository.com上 jogl很久没更新了，最近一次是2015年, 怕是已经没人维护了。

选择lwjgl试试



[https://www.lwjgl.org/guide](https://www.lwjgl.org/guide) 官网好看



官方demo :[https://github.com/LWJGL/lwjgl3-demos](https://github.com/LWJGL/lwjgl3-demos)


可以修改mainclass 查看不同的demo效果
```
  <transformers>
	<transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
     <mainClass>org.lwjgl.demo.${class}</mainClass>
     </transformer>
  </transformers>
```

可是这个是一个专门的window， 可以播放opengl。 希望导出一个图片，后续对opengl有研究了再继续这个项目。

