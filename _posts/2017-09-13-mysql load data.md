---
layout: post
title: "mysql load data"
date: 2017-09-13 13:25:06 +0800
comments: true
category: mysql
tag: [mysql]
---

load data infile "/data/xlive/tools/masterdata/badwords.csv" into table xlive.badwords character set utf8 FIELDS ESCAPED BY    '\\' TERMINATED BY     ',' OPTIONALLY ENCLOSED BY '\'' LINES TERMINATED BY   '\n' ignore 1 lines;


ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement 
错误咋办？

在/etc/my.cnf中添加
secure-file-priv="/data/xlive/tools/masterdata/"

注意： csv文件中，逗号隔开，不要有空格

 