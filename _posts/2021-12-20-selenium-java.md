---
layout: post
title: "java-selenium"
date: 2021-12-20 15:25:06 +0800
comments: true
category: java
tag: [java]
---

# 下载 chrome-driver
https://chromedriver.chromium.org/downloads

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