---
layout: post
title: "mobileProvision"
date: 2016-05-04 10:31:06 +0800
comments: true
category: ios
tag: [ios]
---

No matching provisioning profile found: XXXX, however, no such provisioning profile was found.CodeSign error: code signing is required for product type 'Application' in SDK 'iOS 7.0'

可是~/Library/MobileDevice/Provisioning Profiles是有的

可能用户用到了root中的 所以需要将MobileDevice 整个文件夹拷到/Libraray/下


上传到appStore的时候一直报这个错：Error ITMS-90034:"Missing or invalid signature. The bundle XXX at bundle path 'Payload/XXX.app' is not signed using  an Apple submission certificate."

解决方法: 


1. 查看 Apple Worldwide Developer Relations Certification Authority 这个证书过期了没，重新下载并且将信任置为"使用系统默认"

	查看你的distribution 的mobileProvision 是否用对，并且将信任置为"使用系统默认"。//一般这样就ok了 亲测这个ok


2.如果是用xcodebuild 命令编译的，最好用xcode 的Archive再编译一次重新提交试试 