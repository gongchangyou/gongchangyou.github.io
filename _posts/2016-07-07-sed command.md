---
layout: post
title: "sed替换命令"
date: 2016-07-07 13:24:06 +0800
comments: true
category: shell
tag: [shell]
---

<a herf="http://my.oschina.net/shelllife/blog/118337"> http://my.oschina.net/shelllife/blog/118337</a>

下面是网上找到的一些用法，经实践效果是各不相同的，只有一种是完全可行的。

sed ':label;N;s/\n/:/;b label' filename

sed ':label;N;s/\n/:/;t label' filename

上面的两条命令可以实现将文件中的所有换行符替换为指定的字串，如命令中的冒号。命令的解释：

:label;  这是一个标签，用来实现跳转处理，名字可以随便取(label),后面的b label就是跳转指令

N;  N是sed的一个处理命令，追加文本流中的下一行到模式空间进行合并处理，因此是换行符可见

s/\n/:/;   s是sed的替换命令，将换行符替换为冒号

b label  或者 t label    b / t 是sed的跳转命令，跳转到指定的标签处

标签跳转和N的追加命令实现了每一行的不间断放入模式处理空间，从而不会漏掉每一个换行符，而没有标签的话跳转的话，就只能每两行替换掉一个换行符，对比效果：

$  echo "a,b,c,d" |sed 's/,/\n/g'|sed ':x;N;s/\n/,/;b x'

a,b,c,d

$  echo "a,b,c,d" |sed 's/,/\n/g'|sed 'N;s/\n/,/'

a,b

c,d


<a herf="http://blog.chinaunix.net/uid-26150691-id-3054317.html">sed awk grep 筛选  http://blog.chinaunix.net/uid-26150691-id-3054317.html</a>

<a herf="https://www.evernote.com/l/AKkZj1NmrPFIrIdnucubuEjEaLtAI5KjeVc"> sed awk grep 筛选 https://www.evernote.com/l/AKkZj1NmrPFIrIdnucubuEjEaLtAI5KjeVc</a>


SVN_VERSION=`svn info |sed -n 's/Revision: \(.*\)/\1/p'` #shell结果输出到变量 用`符号即可



	#在ssh之前先整理好参数 只要在ssh中 '${ESCAPE_PATH}'即可
	SCRIPT_DIR=/var/home/aiming/world_all/bin/
	ESCAPE_PATH=$(echo ${SCRIPT_DIR}tmp |sed 's/\//\\\//g')
	echo ${ESCAPE_PATH}
	ssh aiming@lfs-cn-manage "
	cd ${SCRIPT_DIR}
	echo '${PUSH_CONTENT}' > ${SCRIPT_DIR}tmp
	
	./idc_world_all_update.sh update_notification '${ESCAPE_PATH}' 
	"
	#'\/var\/home\/aiming\/world_all\/bin\/tmp'

