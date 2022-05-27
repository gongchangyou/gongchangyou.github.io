---

layout: post
title: "vs code"
date: 2022-05-26 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# VS code in windows

参考文章： [https://blog.csdn.net/weixin_44211968/article/details/122605298](https://blog.csdn.net/weixin_44211968/article/details/122605298)

[https://blog.csdn.net/u014374175/article/details/81365130](https://blog.csdn.net/u014374175/article/details/81365130)

官网下载: [https://code.visualstudio.com/](https://code.visualstudio.com/)



1.  File -> preferences -> settings  各种配置

    1.  配置 Maven: UserSettings  改成本地的settings.xml  [settings-personal.xml]({{ site.baseurl}}/images/202205/settings-personal.xml)
    2.  配置 java home 和 maven path 
    ![]({{ site.baseurl}}/images/202205/20220528000432.png){: width="800" }
    ###  [一个settings.json的例子]({{ site.baseurl}}/images/202205/settings.json)
2.  创建spring 项目

    >   1. 打开命令选项板「Ctrl + Shift + P」；
    >   2. 键入Spring Initializr 开始生成Maven项目；
    >   3. 按照向导执行，选择依赖包，我在这选了 devTools（热部署扩展包）和 web两个。

3.  配置启动项 点击「run」界面中下拉框 -> 「添加配置」按钮，VS Code 会给你自动配好。

4.  调试
    ![]({{ site.baseurl}}/images/202205/20220528000444.png){: width="800" }
