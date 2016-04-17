---
layout: post
title: "mysql知识"
date: 2014-04-28 16:25:06 +0800
comments: true
category: mysql
tag: [mysql]
---

<h3>批量导入sql文件到db中</h3>
ls -1 *.sql | awk ‘{ print “source”,$0 }’ | mysql –force -h192.168.1.22 -P3310 -uroot -ppassword dbname
