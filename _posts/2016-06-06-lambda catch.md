---
layout: post
title: "lambda捕获"
date: 2016-06-06 10:31:06 +0800
comments: true
category: c++
tag: [c++]
---

int x = 1, y=2;

auto z = [x,y]{return x+ y;};

//auto z = [x,&y]{return x+ y;}; //引用就是运行时，值就是编译时

x= 3, y = 4;

cout << z() << endl;


[]捕获的如果是weak_ptr 貌似会丢失? why?


