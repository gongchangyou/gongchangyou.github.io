---
layout: post
title: "csv文件导入mysql"
date: 2017-09-13 13:25:06 +0800
comments: true
category: mysql
tag: [mysql]
---

load data infile "/data/xlive/tools/masterdata/badwords.csv" <br>
into table xlive.badwords character set utf8 FIELDS ESCAPED BY    '\\' TERMINATED BY     ',' OPTIONALLY ENCLOSED BY '\'' LINES TERMINATED BY   '\n' ignore 1 lines;


ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement 
错误咋办？

在/etc/my.cnf中添加
secure-file-priv="/data/xlive/tools/masterdata/"

<font style="color:red">注意： csv文件中，逗号隔开，不要有空格</font>
<br>
<br>
<a href="http://www.51testing.com/html/46/262846-837305.html">替换mysql中某个字段的部分内容</a>

将cdb_pms表subject字段中的Welcom to替换成 欢迎光临  
UPDATE `cdb_pms`  
SET `subject` = REPLACE(`subject`, 'Welcome to', '欢迎光临')  
WHERE INSTR(`subject`,'Welcome to') > 0