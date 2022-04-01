---
layout: post
title: "Postgresql"
date: 2022-03-01 10:05:06 +0800
comments: true
category: mysql
tag: [mysql]
---



#  Postgresql  

### ubuntu 安装 postgresql
[https://linux.cn/article-11480-1.html](https://linux.cn/article-11480-1.html)

1. sudo apt update
2. sudo apt install postgresql postgresql-contrib
3. service postgresql start
4. sudo -s postgresql	 #切换用户
5. pgsql 	#登录PostgreSQL
6. ALTER USER postgres WITH PASSWORD 'password'; # 初始化密码  


### 安装GUI  pgadmin

1. docker pull dpage/pgadmin4

2. docker run -d -p 5433:80 --name pgadmin4 -e PGADMIN_DEFAULT_EMAIL=test@123.com -e PGADMIN_DEFAULT_PASSWORD=123456 -v /var/lib/pgadmin:/var/lib/pgadmin dpage/pgadmin4 

   注意这里的volume 绑定的位置，未来导入导出csv 可以使用

3. 网页打开Localhost:5433 输入第二步的用户名密码

   问题： create server 时发现 无法连接server

   解决：去 /etc/postgresql/12/main下 修改 postgresql.conf 文件

   ```
   listen_addresses = '*' 
   ```

   重启postgresql : service postgresql restart

   

   问题2： no pg_hba.conf entry for host "172.17.0.4", ... SSL on connection to server at "localhost", port 5432

   编辑 pg_hba.conf文件 

   把报错中的host 拷贝到这个文件中：如下编写

   ```
   vi /etc/postgresql/12/main/pg_hba.conf
   
   host all all 172.17.0.4/24 md5
   ```

   

4. 把 安装postgresql 设置好的password 填入即可 这个界面还蛮好看的


![set server list code]({{ site.baseurl}}/images/202202/WechatIMG31.png){: width="800" }

