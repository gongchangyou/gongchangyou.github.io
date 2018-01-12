---
layout: post
title: "修改mysql默认密码 并且解决Can’t connect to local MySQL 问题"
date: 2018-01-12 10:25:06 +0800
comments: true
category: mysql
tag: [mysql]
---

1. mysql stop

2. mysqld_safe --user=mysql --skip-grant-tables --skip-networking &

3. mysql -u root mysql

4. update user set authentication_string=password('root') where user='root';

5. flush privileges;

6. unset TMPDIR

7. mysql_install_db --verbose --user=\`whoami\` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql

如果这一步提示 /usr/local/var/mysql 已经存在，先无情rm掉

8. mysql.server start

9. mysql -uroot -proot 进去啦！！