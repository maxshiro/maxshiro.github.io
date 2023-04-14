---
title: dvwa安装指引
date: 2023-03-07 10:03:53
tags:
- linux
- kali
- dvwa
- fedora
- mysql
---
# 启动nginx
service nginx start
cd /var/www/html
vim hello.html
---
Helloworld!
// 访问虚拟机地址。

```bash bash
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
