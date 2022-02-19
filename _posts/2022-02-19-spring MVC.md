---
layout: post
title: "spring mvc"
date: 2022-02-19 10:05:06 +0800
comments: true
category: java
tag: [java]
---



#  spring MVC 获取url路径和读取本地文件


## 获取路径
```
@RequestMapping("/{animal}/{fileName}")
    public String getJson(
            @PathVariable String animal,
            @PathVariable String fileName
    ) {
```



## 读取本地文件

```
    String fullPath = String.format(directory, animal, fileName);
	String content = Files.readString(Paths.get(fullPath));
```

