---
layout: post
title: "voice"
date: 2022-02-20 10:05:06 +0800
comments: true
category: python
tag: [python]
---



#  python AI语音合成

https://mp.weixin.qq.com/s/ROETeFo4Q__8hkX9Ss-CAw 看到这篇报道。觉得可以试试将自己的语音变成语音包，这样就可以给自己读文章了，或者去喜马拉雅领任务。



pyttsx3是一套基于实现SAPI5文语合成引擎的Python封装库，该库的设计者为Natesh M Bhat，该库其实是 [pyTTS](https://pypi.org/project/pyTTS/) 和 [pyttsx](https://github.com/RapidWareTech/pyttsx) 项目的延续，pyttsx3主要是为Python3版本设计的，但同时也兼容Python2。JaysPySPEECH使用的是pyttsx3 2.7。



但是因为依赖的是当前PC的语音环境，所以无法将我们自己的语音导入。

