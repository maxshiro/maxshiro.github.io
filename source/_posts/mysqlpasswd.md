---
title: 解决mysql第一次安装后改密码的各种问题
date: 2022-10-19 10:16:22
cover: /img/mysqlpasswd/20221019101821.png
tags:
- mysql
- passwd
- linux
---
# 如何重置mysql密码？
按照提示，alter user。
```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```
![](/img/WebBackcreat1/20221019100609.png)  
哈哈快乐报错。

更改密码复杂度。
```bash
set global validate_password_policy=0;
set global validate_password_length=1;
```
提示：
```bash
ERROR 1193 (HY000): Unknown system variable 'validate_password_policy'
```
很快乐。

那就先查看密码规范：
```bash
SHOW VARIABLES LIKE 'validate_password%';
```
提示：
```bash
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```
套娃呢是吧。

解决方法：
重置一个和随机密码差不多的就可以了：
```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'qazWsx>+&3ed';
```
已经提示成功了：
```bash
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'qazWsx>+&3ed';
Query OK, 0 rows affected (0.02 sec)
```
更改密码复杂度：
```bash
set global validate_password.policy=0;
set global validate_password.length=6;
```
修改密码并退出：
```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
exit
```
然后就可以愉快重启登陆了：
```bash
systemctl restart mysqld
mysql -u root -p
```
