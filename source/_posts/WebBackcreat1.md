---
title: 在centos中部署后端程序。
date: 2022-10-16 21:19:32
cover: /img/WebBackcreat1/20221016212408.png
tags:
- linux
- centos
- web
- nginx
- mysql
---
# Intro
> 其实部署很简单，重要的地方在于部署时出现差错后该如何进行处理。
# Nginx的安装
使用源码编译安装：
1. 访问[Nginx](https://nginx.org/en/download.html)官网，复制最新版本链接。wget到本地目录。
> ![](/img/WebBackcreat1/20221016212408.png)  
```bash bash
wget https://nginx.org/download/nginx-1.23.1.tar.gz ~/
```
2. 解压出来。
```bash bash
tar -zxvf nginx-1.23.1.tar.gz
// 如果这里是 *.tar.bz2 的话则使用 -jxvf
```
3. 安装编译所需要的前置。
```bash bash
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```
4. 进入解压出来的文件夹，编译安装。
```bash bash
cd nginx-1.23.1
./configure
make
make install
```
5. 执行nginx
```bash bash
cd /usr/local/nginx/sbin
./nginx
```
> 建议在执行前先使用ps -aux | grep 80查看是否有端口占用80。
6. 写入系统服务
```bash bash
systemctl enable nginx
systemctl status nginx
systemctl start nginx
```
# Mysql 的安装
```bash bash
wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
yum -y localinstall mysql80-community-release-el7-7.noarch.rpm
yum -y install mysql-community-server
```
```bash bash
// 到日志中找到临时密码，然后使用这条命令登录。
mysql -u root -p
```