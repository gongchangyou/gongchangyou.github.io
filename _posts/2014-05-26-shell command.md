---
layout: post
title: "shell 常用命令"
date: 2014-05-26 16:25:06 +0800
comments: true
category: shell
tag: [shell]
---

<h3>批量正则rename</h3>
rename 's/([0-9]).txt/file_$1.txt/' *.txt

<h3>tar命令</h3>

压缩率更大的压缩:tar cvjf  解压:tar xvjf tar.bz2

压缩:tar cvzf 解压:tar xvzf tar.gz

如果有不想进包的部分

tar -zcvf test.tar.gz –exclude=fileName/dirName test

注意这个exclude的位置， 文件名和目录名可以添加正则*来匹配

