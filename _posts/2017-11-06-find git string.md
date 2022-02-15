---
layout: post
title: "查找git中有特定字符串的文件"
date: 2017-11-06 10:25:06 +0800
comments: true
category: git
tag: [git]
---

查看我的修改中是否含有var_dump字符串

git log \-\-name-only \-\-author=\"mouse\" \|grep php \|xargs find {} \|xargs grep var_dump


find . |xargs grep -ri "example_str" -l