---
title: 在centos中部署后端程序。
date: 2022-10-16 21:19:32
top: false
cover: /img/WebBackcreat1/20221016212408.png
tags:
- linux
- centos
- web
- nginx
- mysql
---
# Intro
LAMP和LNMP都是用于搭建Web服务器环境的软件栈，其中LAMP是使用Linux操作系统、Apache Web服务器、MySQL数据库和PHP编程语言，而LNMP则是使用Linux操作系统、Nginx Web服务器、MySQL数据库和PHP编程语言。当然也有组合使用的LNMPA、LAMN、LEMP等，关于这些我只谈谈LNMPA。

## 都是啥东西？

-   L 是指Linux操作系统：LAMP中的L指的是Linux操作系统，通常使用Ubuntu、Debian、CentOS等常见的Linux发行版。
-   A 是指Apache Web服务器：Apache是一个流行的开源Web服务器软件，它支持多种操作系统和编程语言，包括PHP、Perl、Python等。
-   N 是指Nginx Web服务器：Nginx是另一种流行的开源Web服务器软件，它与Apache相比更轻量级、更高效，并且支持更多的并发连接。
-   M 是指MySQL数据库：MySQL是一个流行的关系型数据库管理系统，它支持多种操作系统和编程语言，包括PHP、Java、Python等。
-   P 是指PHP编程语言：PHP是一种流行的Web编程语言，它可以在服务器端生成动态网页内容，与MySQL数据库和Apache Web服务器紧密集成。

总的来说，LAMP和LNMP的最大区别在于Web服务器组件的选择。对于需要处理大量并发连接的Web应用程序，LNMP中的Nginx服务器通常比Apache性能更好。而对于一些较小的Web应用程序，使用LAMP也是一种不错的选择。

## LNMPA
LNMPA同样是由上述的软件组成的。LNMPA的特点是同时支持Nginx和Apache两种Web服务器，可以灵活地根据实际需求进行选择使用。具体的区别在于LNMPA中的N和A的作用。

-   Nginx Web服务器：Nginx是LNMPA中的主要Web服务器，它提供高性能的反向代理和负载均衡功能，可以有效地处理大量并发请求。
-   Apache Web服务器：Apache是LNMPA中的备用Web服务器，当需要使用一些只支持Apache的模块或应用程序时可以切换到Apache。

与LNMP相比，LNMPA的主要优点是可以同时支持Nginx和Apache两种Web服务器，从而更好地适应各种Web应用程序的需求。同时，LNMPA还可以通过配置文件灵活地选择使用哪种Web服务器，并且可以在不重启服务的情况下切换服务器，提高了系统的可用性和灵活性。

## 其他

是否有LAMPN这种组合呢？

LAMPN并不是一个常见的Web服务器软件栈，通常情况下不会使用它来搭建Web服务器环境。不过，如果你指的是在LAMP中加入Nginx Web服务器，也就是使用Linux操作系统、Apache Web服务器、MySQL数据库和Nginx Web服务器，那么这个组合通常被称为LAMN或LEMP。

这里不做详细赘述。

---

# 环境搭建

> 其实部署很简单，重要的地方在于部署时出现差错后该如何进行处理。

## 简单使用

> 使用apt或者yum安装的方式太过简单所以不做详细描述。我只放出命令提供复制使用。

安装 Nginx：

```bash
yum install nginx
```

安装 Apache：

```bash
yum install httpd
```

安装 PHP：

```bash
yum install php
```

以上命令将会安装最新版本的软件包。如果需要安装特定版本的软件，可以在命令后面加上软件包的版本号，例如：

```bash
yum install nginx-1.20.1
yum install httpd-2.4.46
yum install php-7.4.24
```

安装完成后，启动服务并设置开机自启：

```bash
systemctl start nginx/httpd/php-fpm   # 启动服务
systemctl enable nginx/httpd/php-fpm  # 设置开机自启
```

## Nginx的安装

### 使用源码编译安装：

1. 访问[Nginx](https://nginx.org/en/download.html)官网，复制最新版本链接。wget到本地目录。

> ![](./img/WebBackcreat1/20221016212408.png)  

```bash
cd ~
wget https://nginx.org/download/nginx-1.23.1.tar.gz ~/
```

2. 解压出来。

```bash
tar -zxvf nginx-1.23.1.tar.gz
// 如果这里是 *.tar.bz2 的话则使用 -jxvf
```

3. 更新并安装编译所需要的前置。（PS：请使用root用户）

```bash
yum update && yum -y upgrade
yum -y install gcc make gcc-c++ zlib zlib-devel pcre-devel openssl openssl-devel expat-devel
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
```

6. 创建目录

```bash
mkdir /var/temp/nginx -p
```

> 建议在执行前先使用netstat -aux | grep 80查看是否有端口占用80。

### 写Systemd实现开机自启

1. 编辑文件

```bash
cd /lib/systemd/system/
vim nginx.service
```

2. 添加内容

```vim
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
KillStop=/usr/local/nginx/sbin/nginx -s stop
ExecStop=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

其中，`PIDFile`、`ExecStartPre`、`ExecStart`、`ExecReload`、`KillStop`和`ExecStop`等选项需要根据你实际安装的Nginx路径进行修改。

3. 保存并重新加载
```bash
sudo systemctl daemon-reload
```

5. 启动
```bash
// 启动/关闭nginx
systemctl start nginx
systemctl stop nginx
// 开启和关闭开机自启
systemctl enable nginx
systemctl disable nginx
```

## 编译安装 Apache

### 安装依赖

在开始编译安装 Apache 之前，我们需要先安装一些依赖软件包，以确保编译过程顺利进行。在终端中执行以下命令，如果在安装nginx的时候已经安装过的包会自动跳过。

```bash
yum install -y gcc make apr-devel apr-util-devel pcre-devel
```

### 编译安装 APR 库

APR（Apache Portable Runtime）是 Apache 在不同平台上实现跨平台性的工具库，而 Apache HTTP 服务器就依赖于 APR 库。

1. 使用 wget 命令下载 APR 库的源代码包：

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/apache/apr/apr-1.6.5.tar.gz
```

2. 使用 tar 命令解压缩源代码包：

```bash
tar -zxvf apr-1.6.5.tar.gz
```

3. 进入解压后的目录：

```bash
cd apr-1.6.5
```

4. 执行以下命令编译并安装 APR 库：

```bash
./configure --prefix=/usr/local/apr
make && make install
```

其中，`--prefix` 选项指定了安装路径。这里我们将 APR 安装到 `/usr/local/apr` 目录下。

### 编译安装 APR-util 库

APR-util 是 APR 库的补充，它提供了一些 Apache HTTP 服务器所需的额外功能。在编译安装 Apache 时，需要使用 APR-util 库。

1. 使用 wget 命令下载 APR-util 库的源代码包：

```bash
wget https://dlcdn.apache.org//apr/apr-util-1.6.3.tar.gz
```

2. 同上，使用 tar 命令解压缩源代码包：

```bash
tar -zxvf apr-util-1.6.3.tar.gz
```

3. 进入解压后的目录：

```bash
cd apr-util-1.6.3
```

4. 执行以下命令编译并安装 APR-util 库：

```bash
./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr/bin/apr-1-config
make && make install
```

其中，`--prefix` 选项指定了安装路径，`--with-apr` 选项指定了 APR 库的安装路径。

### 编译安装 PCRE 库

PCRE（Perl Compatible Regular Expressions）是一款正则表达式库，它为 Apache HTTP 服务器提供了正则表达式支持。

1. 使用 wget 命令下载 PCRE 库的源代码包：

```bash
wget https://nchc.dl.sourceforge.net/project/pcre/pcre/8.45/pcre-8.45.tar.gz
```

2. 使用 tar 命令解压缩源代码包：

```bash
tar -zxvf pcre-8.45.tar.gz
```

3. 进入解压后的目录：

```bash
cd pcre-8.45
```

4. 执行以下命令编译并安装 PCRE 库：

```bash
./configure --prefix=/usr/local/pcre --with-apr=/usr/local/apr/bin/apr-1-config
make && make install
```

其中，`--prefix` 选项指定了安装路径，`--with-apr` 选项指定了 APR 库的安装路径。

### 安装 Apache

现在，我们已经准备好了编译 Apache 的环境。
1. 使用 wget 命令下载 Apache 的源代码包：

```bash
wget https://mirrors.aliyun.com/apache/httpd/httpd-2.4.57.tar.gz
```

2. 使用 tar 命令解压缩源代码包：

```bash
tar -zxvf httpd-2.4.57.tar.gz
```

3. 进入解压后的目录：

```bash
cd httpd-2.4.57
```

4. 执行以下命令编译并安装 Apache：

```bash
./configure --prefix=/usr/local/httpd --with-pcre=/usr/local/pcre --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util
make && make install
```

其中，`--prefix` 选项指定了安装路径，`--with-pcre`选项指定了 PCRE 库的安装路径，`--with-apr` 和 `--with-apr-util` 选项分别指定了 APR 库和 APR-util 库的安装路径。

注意：如果在编译过程中出现任何错误，请参考错误提示进行解决。

### 配置开机自启动

为了让 Apache 在服务器启动时自动启动，我们需要将其配置为开机自启动服务。

1. 将 apachectl 命令复制到 /etc/init.d/ 目录下：

```bash
cp /usr/local/httpd/bin/apachectl /etc/init.d/httpd
```

2. 将以下命令添加到 /etc/profile 文件末尾：

```bash
echo -e '\nexport PATH=/usr/local/httpd/bin:/usr/local/httpd/lib:$PATH\n' >> /etc/profile && source /etc/profile
```

3. 创建开机自启动服务：

```bash
echo "#chkconfig:2345 64 36" > /etc/rc.d/init.d/httpd
chkconfig --add httpd
chkconfig httpd on
```

现在，Apache 就已经配置为开机自启动服务了。

### 配置防火墙

为了能够让 Apache 监听 HTTP 和 HTTPS 端口，我们需要在服务器的防火墙中添加相应的规则。在终端中执行以下命令：

```bash
// 放行80，443端口
firewall-cmd --add-port=80/tcp --permanent
firewall-cmd --add-port=443/tcp --permanent
// 重新加载防火墙
firewall-cmd --reload
```

这将添加 HTTP 端口（80）和 HTTPS 端口（443）的防火墙规则，并且将规则永久保存。

```bash
//使用如下命令展示所有放行的端口。
firewall-cmd --all
```

如果你觉得放行端口太麻烦，你也可以直接选择关掉防火墙。

```bash
// 关闭防火墙
systemctl stop firewalld
// 直接关闭开机启动
systemctl disable firewalld
```

### 配置 Apache

如果上面的步骤都执行完了，并且修复了错误。那么接下来，我们需要对 Apache 进行一些配置，以确保其能够正常工作。

1. 使用 vim 或其他编辑器打开 Apache 的配置文件：

```bash
vim /usr/local/httpd/conf/httpd.conf
```

2. 将以下行的注释符号（#）去掉：

```
#ServerName www.example.com:80
```

这将允许 Apache 监听所有可用的网络接口，并且可以响应来自任何主机的请求。

3. 保存并关闭文件。

### 启动 Apache

使用以下命令启动 Apache 服务：

```bash
systemctl start httpd
```

现在，你可以通过浏览器访问服务器的 IP 地址或域名，验证 Apache 是否已经成功安装和配置。

如果一切正常，你应该能够看到 Apache 的默认欢迎页面。

such as：
>![](Pasted image 20230519161454.png)


## 编译安装PHP

### 安装依赖

安装 epel-release 源

```bash
yum -y install epel-release
```

更新系统

```bash
yum update
```

安装 PHP 所需依赖

```bash
yum -y install php-mcrypt libmcrypt libmcrypt-devel autoconf freetype gd jpegsrc libmcrypt libpng libpng-devel libjpeg libxml2 libxml2-devel zlib curl curl-devel openssl openssl-devel sqlite-devel
```

安装 oniguruma 库

```bash
yum -y install http://down.24kplus.com/linux/oniguruma/oniguruma-6.7.0-1.el7.x86_64.rpm
yum -y install http://down.24kplus.com/linux/oniguruma/oniguruma-devel-6.7.0-1.el7.x86_64.rpm
```

### 下载 PHP

下载 PHP 源码包

```bash
wget https://www.php.net/distributions/php-8.2.5.tar.gz
```

解压源码包

```bash
tar -zxvf php-8.2.5.tar.gz
```

进入源码包目录

```bash
cd php-8.2.5
```

### 配置 PHP 并编译安装
使用以下命令配置编译选项：

```bash
./configure --prefix=/usr/local/php8/ --with-config-file-path=/usr/local/php8/etc/ --with-apxs2=/usr/local/httpd/bin/apxs --enable-mbstring --with-curl -with-gd --enable-fpm --enable-mysqlnd --with-pdo-mysql=mysqlnd --with-mysqli=mysqlnd
```

或者将上述命令分成多行，更易读：

```bash
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
```

编译并安装 PHP

```bash
make && make install
```

### 配置环境变量和测试

将 PHP 加入系统 PATH 环境变量

```bash
echo -e '\nexport PATH=/usr/local/php8/bin:/usr/local/php8/sbin:$PATH\n' >> /etc/profile && source /etc/profile
```

测试 PHP-FPM 配置是否正确

```bash
php-fpm -t
```

如果配置正确，会输出如下信息：

```
[18-Apr-2023 10:12:57] NOTICE: configuration file /usr/local/php8/etc/php-fpm.conf test is successful
```

### 启动和关闭 PHP-FPM

启动 PHP-FPM

```bash
/etc/init.d/php-fpm
```

关闭 PHP-FPM

```bash
pkill php-fpm
```

### 配置 Apache

编辑 Apache 配置文件，使其可以解析php。

```bash
vi /usr/local/httpd/conf/httpd.conf
```

1. 加载 PHP 模块

查看是否已经添加了，如果没有在 loadModule 处添加。正确安装后，应该已经有了。

```
LoadModule php7_module modules/libphp7.so
```

2. 设置默认文档

在 `<IfModule dir_module>` 模块添加 `index.php`，作为默认文档。

```
DirectoryIndex index.html index.php
```

3. 添加 PHP 文件类型

在 Apache 配置文件中找到以下内容：

```
AddType application/x-compress .Z
AddType application/x-gzip .gz .tgz
```

添加如下内容：

```
AddType application/x-httpd-php-source .phps
AddType application/x-httpd-php .php
```

### 重启 Apache

重启 Apache 使配置生效

```bash
systemctl restart httpd
```

### 测试

在 Apache 根目录下创建一个 `index.php` 文件，内容如下：

```php
<?php phpinfo(); ?>
```

在浏览器中访问：`http://your_domain/index.php`，如果看到 PHP 信息页，说明 PHP 已经成功安装和配置。


![](./img/WebBackcreat1/20230421154601.png)

## Mysql 的安装

Mysql我就不用编译安装了，有点折磨。

1. 先安装wget `yum install wget`。

2. 下载并安装mysql。

```bash

wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm

yum -y localinstall mysql80-community-release-el7-7.noarch.rpm

yum -y install mysql-community-server

```

3. 启动mysql

```bash

// 启动mysql

systemctl start mysqld

// 写入开机启动

systemctl enable mysqld

```

4. 查找日志并找到临时密码。

```bash

vi /var/log/mysqld.log

```

> ![](./img/WebBackcreat1/20221019082932.png)  


5. 登录mysql

```bash

mysql -u root -p

// 密码则为查找到的临时密码。

password:

```

6. 修改密码

> 我有专门说过这傻逼mysql改密码。可以到我的博客上看看。

7. 重启mysql服务

```bash

service mysqld restart

```

> 重启后就可以正常使用mysql了。

## 访问之前

> 上面有提到端口开放，可以用这条命令看看是否放开了：

```bash
firewall-cmd --all
```

---
# 安装web程序

安装好LAMP/LNMP环境后就可以安装和使用对应环境的网页程序了，这里给出一些常见的环境。

## 安装pma

phpMyAdmin（简称 PMA）是一款基于 PHP 的 MySQL 数据库管理工具，可以通过 Web 界面对 MySQL 数据库进行管理，包括创建、修改和删除数据库、表、字段、索引等操作，同时还支持 SQL 查询和导入导出数据等功能，是 MySQL 管理的重要工具之一。

1. 下载 PMA：

```bash
wget https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.zip
```

2. 解压 PMA：

```bash
unzip phpMyAdmin-5.2.1-all-languages.zip
```

3. 重命名解压后的目录：

```bash
mv phpMyAdmin-5.2.1-all-languages pma
```

4. 安装 PHP mysqli 扩展：

```bash
yum install php-mysqli
```

5. 进入 PMA 目录：

```bash
cd pma
```

6. 复制配置文件：

```bash
cp config.sample.inc.php config.inc.php
```

7. 修改配置文件：

```bash
vi config.inc.php
```

在该文件中，您需要修改以下参数：

```php
$cfg['Servers'][$i]['host'] = 'localhost'; // 数据库主机地址
$cfg['Servers'][$i]['port'] = '3306'; // 数据库端口号
$cfg['Servers'][$i]['user'] = 'root'; // 数据库用户名
$cfg['Servers'][$i]['password'] = 'your_password'; // 数据库密码
$cfg['bluefish'] = ''; // 用于加密的密钥，可以留空
```

将上述参数修改为您的 MySQL 数据库的实际配置即可。如果您的 MySQL 数据库与 PMA 安装在同一台服务器上，那么主机地址可以设置为 `localhost`，端口号可以设置为默认的 `3306`。如果您的 MySQL 数据库与 PMA 安装在不同的服务器上，那么主机地址应该设置为 MySQL 数据库的 IP 地址，端口号可以根据实际情况进行修改。

8. 配置 HTTP 访问：

将 PMA 目录放置在 Apache 或 Nginx 的根目录下，例如 `/var/www/html`，然后通过 HTTP 协议访问，例如 `http://your_domain/pma`。

如果您使用的是 Apache，还需要在 Apache 配置文件中添加以下内容：

```apache
Alias /pma /var/www/html/pma
<Directory /var/www/html/pma>
    Options FollowSymLinks
    DirectoryIndex index.php
    Require all granted
</Directory>
```

9. 重启 HTTP 服务器：

```bash
systemctl restart httpd/nginx
```

至此，PMA 已经安装完成，并可以通过HTTP 协议访问。请访问 `http://your_domain/pma`，使用您的 MySQL 数据库账号和密码登录 PMA。

### PMA

在这里安装时并没有创建如虚拟主机这种配置文件，而是直接放在默认的网站目录里。下面安装wordpress我将会创建一个虚拟主机，来安装和使用wordpress。

## 安装wordpress

WordPress 是一款流行的开源博客系统，它基于 PHP 和 MySQL 构建，具有简单易用、灵活可扩展等优点，是搭建个人博客、企业网站等的不二之选。

我将详细介绍如何安装wordpress。
### 创建虚拟网站
为了避免html下文件之间的互相影响，可以选择创建虚拟网站来将他们隔开。
#### Apache方法

首先，需要在 Apache 的配置文件中创建一个虚拟主机。假设我们要为 WordPress 创建一个名为 `example.com` 的虚拟主机，可以按照以下步骤进行配置：

1. 创建一个新的配置文件 `/etc/httpd/conf.d/example.com.conf`，并添加以下内容：

```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/wp

    <Directory /var/www/wp>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    <FilesMatch \.php$>
        SetHandler "proxy:fcgi://127.0.0.1:9000"
    </FilesMatch>
</VirtualHost>
```

上述配置文件中，`ServerName` 指定了虚拟主机的域名，`DocumentRoot` 指定了虚拟主机的根目录，`<Directory>` 定义了虚拟主机的访问控制和索引文件等配置，`<FilesMatch>` 则用于匹配 PHP 脚本的 URL，并使用 FastCGI 处理 PHP 脚本。需要将其中的 `/var/www/example.com` 替换为实际的 WordPress 根目录。

2. 在 `/etc/hosts` 文件中添加虚拟主机的 IP 地址和域名的映射关系：

```
127.0.0.1 example.com
```

3. 重启 Apache 服务：

```
systemctl restart httpd
```

需要注意的是，如果需要使用 SSL 证书，还需要进行 SSL 相关的配置，并将虚拟主机的端口从 80 改为 443。

#### nginx方式

与 Apache 类似，需要在 Nginx 的配置文件中创建一个虚拟主机。假设我们要为 WordPress 创建一个名为 `example.com` 的虚拟主机，可以按照以下步骤进行配置：

1. 创建一个新的配置文件 `/etc/nginx/conf.d/example.com.conf`，并添加以下内容：

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/example.com;

    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

上述配置文件中，`listen` 指定了虚拟主机监听的端口，`server_name` 指定了虚拟主机的域名，`root` 指定了虚拟主机的根目录，`location /` 用于定义 URL 的匹配规则和相应的处理方式，`location ~ \.php$` 则用于匹配 PHP 脚本的 URL，并使用 FastCGI 处理 PHP 脚本。需要将其中的 `/var/www/example.com` 替换为实际的 WordPress 根目录。

2. 在 `/etc/hosts` 文件中添加虚拟主机的 IP 地址和域名的映射关系：

```vim
127.0.0.1 example.com
```

3. 重启 Nginx 服务：

```
systemctl restart nginx
```

和apache相同，如果需要使用 SSL 证书，还需要进行 SSL 相关的配置，并将虚拟主机的端口从 80 改为 443。

### 安装

1. 创建 MySQL 数据库和用户

登录 MySQL 控制台，并创建一个新的数据库和用户：

```bash
mysql -u root -p

mysql> CREATE DATABASE wordpress_db;
mysql> GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress_user'@'localhost' IDENTIFIED BY 'password';
mysql> FLUSH PRIVILEGES;
mysql> EXIT;
```

其中，'wordpress_db' 是要创建的数据库名，'wordpress_user' 是要创建的数据库用户，'password' 是要设置的数据库密码。需要将这些参数替换为自己的实际值。

2. 下载和解压 WordPress

从 WordPress 官网下载最新的 WordPress压缩包，并解压到 Apache 的 Web 根目录下，例如 `/var/www/html/`：

```bash
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar -zxvf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/
```

3. 配置 WordPress

重命名配置文件：

```bash
cd /var/www/html/
cp wp-config-sample.php wp-config.php
```

编辑 `wp-config.php` 文件，并根据之前创建的数据库和用户设置以下参数：

```php
define('DB_NAME', 'wordpress_db');
define('DB_USER', 'wordpress_user');
define('DB_PASSWORD', 'password');
define('DB_HOST', 'localhost');
```

其中，'wordpress_db' 是之前创建的数据库名，'wordpress_user' 是之前创建的数据库用户，'password' 是之前设置的数据库密码。

4. 设置文件权限

修改 WordPress 文件的权限，以使得 Apache 可以对其进行读写：

```bash
chown -R apache:apache /var/www/html/
chmod -R 755 /var/www/html/
```

5. 启动 Apache 和 MySQL

启动 Apache 和 MySQL 服务，并设置开机自启：

```bash
systemctl start httpd
systemctl start mariadb
systemctl enable httpd
systemctl enable mariadb
```

6. 访问 WordPress

现在，我们可以通过浏览器访问 WordPress，输入服务器 IP 地址或域名，即可开始 WordPress 的安装过程。按照提示，设置网站名称、管理员账号和密码等信息，然后点击“安装 WordPress”按钮，即可完成 WordPress 的安装。

需要注意的是，为了保障网站的安全性，我们需要在 WordPress 安装完成后进行一些安全性方面的设置，例如加强访问控制、配置 SSL 证书、定期更新 WordPress 和插件等等。另外，我们也可以根据需要安装并配置一些 WordPress 插件，以扩展 WordPress 的功能。