---
title: dvwa安装指引
date: 2023-03-07 10:03:53
categories: [linux, web]
cover: /img/dvwaonlinux/2023-06-12-23-14-34.png
tags:
- linux
- kali
- dvwa
- fedora
- mysql
---
## 多说无用，建议直接docker一步到位。
# 启动nginx
service nginx start
cd /var/www/html
vim hello.html
---
Helloworld!
// 访问虚拟机地址。

```bash
[root@fedora axro]# cd /usr/share/nginx/html/
[root@fedora html]# git clone https://github.com/digininja/DVWA.git
[root@fedora html]# cd DVWA/config
[root@fedora config]# cp config.inc.php.dist config.inc.php
[root@fedora config]# vi config.inc.php
mysql -u root -p
create database DVWA;
```

mysql> create database dvwa;
Query OK, 1 row affected (0.00 sec)

mysql> create user dvwa@localhost identified by 'p@ssw0rd';
Query OK, 0 rows affected (0.01 sec)

mysql> grant all on dvwa.* to dvwa@localhost;
Query OK, 0 rows affected (0.01 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
