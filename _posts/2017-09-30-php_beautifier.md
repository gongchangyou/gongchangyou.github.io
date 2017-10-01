---
layout: post
title: "在sublime 2中添加php_beautifer "
date: 2017-09-30 13:25:06 +0800
comments: true
category: php
tag: [php]
---

1.下载go-pear.phar 到php安装目录下，地址http://pear.php.net/go-pear.phar 。//macbook 无法放到/usr/bin 中，我随便放到了/User/gongchangyou/ 下 <br>

2.cd /user/gongchangyou  

    php go-pear.phar
    一路回车

3.export PATH="/Users/gongchangyou/pear/bin:$PATH" 将这行加到~/.bash_profile中，source ~/.bash_profile

4.pear install --alldeps channel://pear.php.net/php_beautifier-0.1.15

5.在Sublime text中安装beautifier插件,  按ctrl+shift+p 在package control中搜索install ,回车后稍等一下就会跳出好多插件,搜索phpbeautifier,回车安装

6.重点来了: 去到sublime => Preferences => Browse Packages 打开PhpBeautifier/php_beautifier.py

    # cmd = "php_beautifier"
    cmd = "/Users/gongchangyou/pear/bin/php_beautifier" #替换成之前第4步安装的php_beautifier的路径
    如果有需求将class和function 的开启花括号放到下一行
    filters = "ArrayNested() NewLines(before=switch:while:for:foreach:T_CLASS:return:break) Pear(add-header=false,newline_class=true,newline_function=true)" //这里需要加上newline_class=true,newline_function=true

7.试试ctrl+alt+f 或者 command+shift+p   搜索format php


