---
layout: post
title: "shader-module"
date: 2016-11-11 13:24:06 +0800
comments: true
category: shader
tag: [shader]
---

标准光照模型

漫反射

C_diffuse = C_light * M_diffuse * max(0, dot(n,l))


高光反射

r = 2(n . l)n - l;

C_specular = C_light * M_specular * power(max(0, dot(v, r)), M_gloss);

C_specular = C_light * M_specular * power(max(0, dot(n, h)), M_gloss);

