---
layout: post
title: "mysql查找多行重复记录"
date: 2017-10-24 10:25:06 +0800
comments: true
category: mysql
tag: [mysql]
---

select user_name,count(*) as count from user_table group by user_name having count>1; 

 

