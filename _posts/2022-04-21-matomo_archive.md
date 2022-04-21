---
layout: post
title: "matomo"
date: 2022-04-21 17:25:06 +0800
comments: true
category: php
tag: [php]
---

#  Matomo archive

研究下这个档案缓存 900s是如何生效的

![]({{ site.baseurl}}/images/202204/WechatIMG114.png){: width="800" }


namespace Piwik\Plugins\Actions; API.getDownloads

这里根据archive返回数据
![]({{ site.baseurl}}/images/202204/WechatIMG113.png){: width="800" }



$minDatetimeArchiveProcessedUTC 就是当前时间减去 900s.
<br>
ArchiveSelector 中判断archive的时间和  $minDatetimeArchiveProcessedUTC 哪个更早，如果archive 的时间更早，则这个archive就失效。

![]({{ site.baseurl}}/images/202204/WechatIMG112.png){: width="800" }

如果archive失效 ，则新增
![]({{ site.baseurl}}/images/202204/WechatIMG116.png){: width="800" }

matomo_archive_blob_2022_04 中的就是对应的二进制缓存
![]({{ site.baseurl}}/images/202204/WechatIMG117.png){: width="800" }