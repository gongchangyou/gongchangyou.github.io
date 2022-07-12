---
layout: post
title: "mysqldump --tz-utc=0 的坑"
date: 2016-08-26 13:24:06 +0800
comments: true
category: mysql
tag: [mysql]
---

mysqldump 时区对不上 用unix_timestamp 在mysql里面运行还是ok的 结果用mysqldump 就不对了


InsertTime >= unix_timestamp(date_sub(curdate(), INTERVAL 1 DAY)) and InsertTime <= unix_timestamp(curdate())


需要用--tz-utc=0 来设置默认时区，否则用的就是mysqldump在默认情况下，是按’+00:00’(中时区).

mysqldump ${MYSQLDUMP_OPTION}  --databases ${database_schema} --tables ${database_table} --skip-opt --quick --single-transaction --hex-blob --no-create-info --tz-utc=0 --where "${mysqldump_where}" \| grep INSERT > ${dump_file}


http://www.linuxidc.com/Linux/2012-12/76184.htm
