---
layout: post
title: "curl常用命令"
date: 2017-12-14 10:25:06 +0800
comments: true
category: shell
tag: [shell]
---


模拟队列插入
curl -d '{"id":853098,"event":"update","timestamp":1463641228622}' 'http://nydus.tj.a.ajkdns.com/publish?tunnel=service.internal.community&routingKey=service.internal.community.data_change'



上传图片
curl -F"file=@/Users/gongchangyou/Downloads/wKhzR1FmuQ-jPj0qAAGMNl9gujE083.jpg" 'http://upd1.anjuke.com/upload'