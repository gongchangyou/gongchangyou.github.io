---
layout: post
title: "maven settings"
date: 2022-06-29 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# maven settings

设置 settings.xml

```
mvn -s "D:\program\maven-3.6.3\maven3\conf\settings.xml" clean install
```

查看当前生效的 settings

```
mvn help:effective-settings
```



mvn命令指定 settings

```
mvn install --settings c:\user\settings.xml 
```

