---
layout: post
title: "java agent"
date: 2022-07-16 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# java agent

参考文章： [https://xz.aliyun.com/t/9450](https://xz.aliyun.com/t/9450)

[https://www.cnblogs.com/rickiyang/p/11368932.html](https://www.cnblogs.com/rickiyang/p/11368932.html)



代码仓库: [https://github.com/gongchangyou/javaagent_test](https://github.com/gongchangyou/javaagent_test)

1.  premain 启动应用的同时 添加 -javaagent:/path/to/agent.jar

    

问题1：Failed to find Premain-Class manifest attribute

回答1:  解压缩我打的包之后发现 MANIFEST.MF文件被替换成了默认的. 我们用maven 插件修改他

```
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Created-By: Apache Maven 3.6.3
Built-By: gongchangyou
Build-Jdk: 11.0.12


```

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
        <archive>
            <!--自动添加META-INF/MANIFEST.MF -->
            <manifest>
                <addClasspath>true</addClasspath>
            </manifest>
            <manifestEntries>
                <Premain-Class>com.mouse.PreDemo</Premain-Class>
                <Agent-Class>com.mouse.AgentDemo</Agent-Class>
                <Can-Redefine-Classes>true</Can-Redefine-Classes>
                <Can-Retransform-Classes>true</Can-Retransform-Classes>
            </manifestEntries>
        </archive>
    </configuration>
</plugin>
```

---



2.  agentmain 应用已经启动的情况下，将agent.jar attach上去

  

  代码仓库: [https://github.com/gongchangyou/agent_main](https://github.com/gongchangyou/agent_main)
  [https://github.com/gongchangyou/agent_demo](https://github.com/gongchangyou/agent_demo)

  ```
      java -jar agent.jar pid /path/to/app.jar
      
      # 一个例子
      java -jar javaagent_test-1.0-SNAPSHOT.jar 40843 /Users/gongchangyou/learn/spring_xml_test/target/spring_xml_test-0.0.1-SNAPSHOT.jar
      
      
  ```



---



问题1： Agent JAR not found or no Agent-Class attribute

回答1：loadAgent ， agent.jar注意用绝对路径



参考文章: [https://blog.csdn.net/weixin_37828719/article/details/106237872](https://blog.csdn.net/weixin_37828719/article/details/106237872)