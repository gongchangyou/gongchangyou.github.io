---
layout: post
title: "java-selenium"
date: 2021-12-20 15:25:06 +0800
comments: true
category: java
tag: [java]
---

# 下载 chrome-driver
[https://chromedriver.chromium.org/downloads](https://chromedriver.chromium.org/downloads)

ChromeDriver 96.0.4664.45 对应 selenium-java版本 3.14.0 (这个版本比较稳定. 最新版本4.1.4 会class not found)

## 拷贝到 /usr/local/bin/ 并 chmod 777 

MacBook-Pro-7:Downloads gongchangyou$ cp chromedriver /usr/local/bin/
MacBook-Pro-7:Downloads gongchangyou$ chmod 777 /usr/local/bin/chromedriver

## 可能需要在“系统偏好设置” -》 “安全性与隐私” 中添加 "仍可打开"

# 添加依赖

```
<dependency>
   <groupId>org.seleniumhq.selenium</groupId>
   <artifactId>selenium-java</artifactId>
   <version>3.14.0</version>
</dependency>
```

# 编写代码

```
        log.info("args = {}", args);
        val driver = new ChromeDriver();
        driver.get("https://leetcode-cn.com/tag/dynamic-programming/problemset/");
        val problemList = driver.findElementsByCssSelector("#lc-content > div > div.css-1ezka95-TableContainer.ermji1u1 > div > section > div > div.css-d8889y-antdPaginationOverride-layer1-dropdown-layer1-hoverOverlayBg-layer1-card-layer1-layer0 > div > div > div > div > div > div > table > tbody > tr:nth-child(1) > td:nth-child(2) > div > div > div");
        log.info("problemList={}", problemList);
```

# 如果难以打开新的标签页

```
JavascriptExecutor js = (JavascriptExecutor) driver;  
js.executeScript("window.open();");

JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("window.open(\"http://www.baidu.com\");");
```

### 有时候会报 chromedriver 支持的chrome版本不匹配。安装和你本地chrome对应的 chromedriver即可 [https://chromedriver.chromium.org/downloads](https://chromedriver.chromium.org/downloads)

```
2022-05-08 21:21:26.810 ERROR [http-nio-8081-exec-1] o.a.c.c.C.[.[localhost].[/].[dispatcherServlet] : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.openqa.selenium.SessionNotCreatedException: session not created: This version of ChromeDriver only supports Chrome version 96
Current browser version is 101.0.4951.54 with binary path /Applications/Google Chrome.app/Contents/MacOS/Google Chrome
```

代码中设置
```
System.setProperty("webdriver.chrome.driver", "/usr/local/bin/chromedriver_101");
```



Selector 选择器语法

[https://blog.csdn.net/kuangjuelian229/article/details/87868816](https://blog.csdn.net/kuangjuelian229/article/details/87868816)



tab的操作

[https://www.browserstack.com/guide/handling-tabs-in-selenium](https://www.browserstack.com/guide/handling-tabs-in-selenium)







阿里云可能需要安装一些依赖：

```
yum install libxcb

yum install https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm
```

