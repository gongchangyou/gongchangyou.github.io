---
layout: post
title: "python keyring 在macos的cron中无效"
date: 2018-04-11 10:25:06 +0800
comments: true
category: python
tag: [python]
---



写了个定时定会议室的脚本，要利用keyring模块获取chrome中的cookie。
在终端中执行python没问题，写到cron中就报错 Can't fetch password from system

1. 解锁钥匙串 在cron中先执行 security unlock-keychain -p MYPASS /path/to/login.keychain

2. 代码中设置钥匙串的路径    keyring.get_keyring().keychain = '/Users/gongchangyou/Library/Keychains/login.keychain-db'

