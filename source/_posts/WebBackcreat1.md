---
title: 在centos中部署后端程序。
date: 2022-10-16 21:19:32
top: false
cover: img/WebBackcreat1/20221016212408.png
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
```bash
wget https://nginx.org/download/nginx-1.23.1.tar.gz ~/
```
2. 解压出来。
```bash
tar -zxvf nginx-1.23.1.tar.gz
// 如果这里是 *.tar.bz2 的话则使用 -jxvf
```
3. 安装编译所需要的前置。
```bash
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```
4. 进入解压出来的文件夹，编译安装。
```bash
cd nginx-1.23.1
./configure
make
make install
```
5. nginx的启动和关闭
```bash
cd /usr/local/nginx/sbin
// 启动nginx
./nginx
// 关闭nginx
./nginx -s stop
// 关闭防火墙
systemctl stop firewalld
```
6. 创建目录
```bash
mkdir /var/temp/nginx -p
```
> 建议在执行前先使用netstat -aux | grep 80查看是否有端口占用80。

7. 开机自启 *还没写完
    1. 编辑文件
    ```bash
    cd /lib/systemd/system/
    vim nginx.service
    ```
    2. 添加内容
    ```bash
    [Unit] 
    Description=nginx 
    service After=network.target 
    [Service] 
    Type=forking 
    ExecStart=/usr/local/nginx/sbin/nginx 
    ExecReload=/usr/local/nginx/sbin/nginx -s reload ExecStop=/usr/local/nginx/sbin/nginx -s quit PrivateTmp=true 
    [Install] 
    WantedBy=multi-user.target
    // 解释
    [Unit]:服务的说明
    Description:描述服务
    After:描述服务类别
    [Service]服务运行参数的设置
    Type=forking是后台运行的形式
    ExecStart为服务的具体运行命令
    ExecReload为重启命令
    ExecStop为停止命令
    PrivateTmp=True表示给服务分配独立的临时空间
    注意：[Service]的启动、重启、停止命令全部要求使用绝对路径
    [Install]运行级别下服务安装的相关设置，可设置为多用户，即系统运行级别为3
    ```
    3. 保存退出
    4. 启动
    ```bash
    systemctl start nginx.service
    systemctl enable nginx.service
    systemctl status nginx.service
    ```
8. 233

# 编译安装 apache
```bash
yum update

yum install -y gcc-c++
yum install -y expat-devel pcre-devel

wget https://mirrors.tuna.tsinghua.edu.cn/apache/apr/apr-1.6.5.tar.gz
tar -zxvf apr-1.6.5.tar.gz
cd apr-1.6.5
./configure --prefix=/usr/local/apr
make && make install

wget https://dlcdn.apache.org//apr/apr-util-1.6.3.tar.gz
tar -zxvf apr-util-1.6.3.tar.gz
cd apr-util
./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr/bin/apr-1-config
make && make install

wget https://nchc.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz
tar -zxvf pcre-8.45.tar.gz
cd pcre-8.45
./configure --prefix=/usr/local/pcre --with-apr=/usr/local/apr/bin/apr-1-config
make && make install

wget https://mirrors.aliyun.com/apache/httpd/httpd-2.4.57.tar.gz
tar -zxvf httpd-2.4.57.tar.gz
cd httpd-2.4.57
./configure --prefix=/usr/local/httpd --with-pcre=/usr/local/pcre --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util
cp /usr/local/httpd/bin/apachectl /etc/init.d/httpd

echo -e '\nexport PATH=/usr/local/apache/bin:/usr/local/apache/lib:$PATH\n' >> /etc/profile && source /etc/profile

echo "#chkconfig:2345 64 36" > /etc/rc.d/init.d/httpd

chkconfig --add httpd
chkconfig httpd on

vim /usr/local/apache/conf/httpd.conf
unsharp #ServerName www.example.com:80

systemctl start httpd
firewall-cmd --add-port=80/tcp
firewall-cmd --add-port=443/tcp
#systemctl stop firewalld
#systemctl disable firewalld




php
yum -y install epel-release
yum update
yum -y install php-mcrypt libmcrypt libmcrypt-devel  autoconf  freetype gd jpegsrc libmcrypt libpng libpng-devel libjpeg libxml2 libxml2-devel zlib curl curl-devel openssl openssl-devel sqlite-devel

yum -y install http://down.24kplus.com/linux/oniguruma/oniguruma-6.7.0-1.el7.x86_64.rpm
yum -y install http://down.24kplus.com/linux/oniguruma/oniguruma-devel-6.7.0-1.el7.x86_64.rpm

wget https://www.php.net/distributions/php-8.2.5.tar.gz
tar -zxvf php-8.2.5.tar.gz
cd php-8.2.5
./configure --prefix=/usr/local/php8/ --with-config-file-path=/usr/local/php8/etc/ --with-apxs2=/usr/local/httpd/bin/apxs --enable-mbstring --with-curl -with-gd --enable-fpm --enable-mysqlnd --with-pdo-mysql=mysqlnd --with-mysqli=mysqlnd
./configure \
--prefix=/usr/local/php8 \
--with-config-file-path=/usr/local/php8/etc \
--with-apxs2=/usr/local/httpd/bin/apxs \
--with-mysql-sock=/usr/local/mysql/mysql.sock \
--with-mysqli \
--with-zlib \
--with-curl \
--with-gd \
--with-jpeg-dir \
--with-png-dir \
--with-freetype-dir \
--with-openssl \
--enable-mbstring \
--enable-xml \
--enable-session \
--enable-ftp \
--enable-pdo \
--enable-tokenizer \
--enable-mysqlnd \
--enable-fpm \
--with-pdo-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--enable-zip
make && make install


echo -e  '\nexport PATH=/usr/local/php8/bin:/usr/local/php8/sbin:$PATH\n' >> /etc/profile && source /etc/profile

php-fpm -t

出现如下提示则正常：
[18-Apr-2023 10:12:57] NOTICE: configuration file /usr/local/php8/etc/php-fpm.conf test is successful

使用如下命令启动php
/etc/init.d/php-fpm
关闭
pkill php-fpm

vi /usr/local/httpd/conf/httpd.conf

1、查看是否已经添加了，如果没有在loadModule处添加 ，正确安装后，应该已经有了
LoadModule php7_module modules/libphp7.so 
2、 在<IfModule dir_module>模块添加index.php
<IfModule dir_module>    
DirectoryIndex index.html index.php
</IfModule> 
3、找到：
AddType  application/x-compress .Z
AddType application/x-gzip .gz .tgz
添加如下内容
AddType application/x-httpd-php-source .phps
AddType application/x-httpd-php .php

systemctl restart httpd
cd /usr/local/httpd/htdocs/
echo "<?php phpinfo(); ?>" > index.php
```
![](/img/WebBackcreat1/20230421154601.png)
# Mysql 的安装
1. 先安装wget `yum install wget`。
2. 下载并安装mysql。
```bash
wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
yum -y localinstall mysql80-community-release-el7-7.noarch.rpm
yum -y install mysql-community-server
```
1. 启动mysql
```bash
// 启动mysql
systemctl start mysqld
// 写入开机启动
systemctl enable mysqld
```
1. 查找日志并找到临时密码。
```bash
vi /var/log/mysqld.log
```
> ![](/img/WebBackcreat1/20221019082932.png)  

1. 登录mysql
```bash
mysql -u root -p
// 密码则为查找到的临时密码。
password:
```

1. 修改密码
> 后面专门说这傻逼mysql改密码。

1. 重启mysql服务
```bash
service mysqld restart
```
> 重启后就可以正常使用mysql了。

# httpd/php安装
```bash
sudo yum update && sudo yum upgrade
systemctl status httpd
sudo yum install httpd
systemctl start httpd
service mysql start
yum install php php-devel
cd /var/www/html/
vi test.php
------
<?php phpinfo();?>
------
systemctl stop firewalld
```
打开浏览器访问
localhost/test.php
出现php信息就成功了。

# 安装pma
```bash
wget https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.zip
unzip phpMyAdmin-5.2.1-all-languages.zip
mv phpMyAdmin-5.2.1-all-languages pma
yum install php-mysqli
cd pma
cp config.sample.inc.php config.inc.php
```
打开浏览器访问
localhost/pma
提示登陆就成功了。
