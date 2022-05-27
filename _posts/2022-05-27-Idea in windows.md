---

layout: post
title: "idea"
date: 2022-05-27 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# Idea in windows

1.  去官网下载  [https://www.jetbrains.com/zh-cn/idea/](https://www.jetbrains.com/zh-cn/idea/)

2.  插件安装 ：`File` -> `Settings` -> `Plugins`.

3.  去 file -> settings 搜索maven 看看 maven的settings.xml 在哪里，备份下，然后把阿里云的源添加进去     [settings-personal.xml]({{ site.baseurl}}/images/202205/settings-personal.xml)

4.  修改idea内存  

    ![]({{ site.baseurl}}/images/202205/idea_vm_options.png){: width="800" }

    下面这段我不知道从哪儿抄来的，其中最有用的就是 设置内存     -Xmx4096m

    ```
    # custom IntelliJ IDEA VM options
    
    -Xms128m
    -Xmx4096m
    -XX:ReservedCodeCacheSize=240m
    -XX:+UseCompressedOops
    -Dfile.encoding=UTF-8
    -XX:+UseConcMarkSweepGC
    -XX:SoftRefLRUPolicyMSPerMB=50
    -ea
    -XX:CICompilerCount=2
    -Dsun.io.useCanonCaches=false
    -Djava.net.preferIPv4Stack=true
    -Djdk.http.auth.tunneling.disabledSchemes=""
    -XX:+HeapDumpOnOutOfMemoryError
    -XX:-OmitStackTraceInFastThrow
    
    -XX:ErrorFile=$USER_HOME/java_error_in_idea_%p.log
    -XX:HeapDumpPath=$USER_HOME/java_error_in_idea.hprof
    
    ```

5.  显示idea使用内存情况  右下角右键 选中 memory indicator (内存指示器)

6.  更改主题 ：   [dark_theme.icls]({{ site.baseurl}}/images/202205/dark_theme.icls)  个人比较喜欢这款糖果配色
file-> settings -> 搜索color scheme -> import 上述文件即可
    

问题1： 为什么我的IDEA是中文？

解决1： 去plugins中把chinese那个插件停用了