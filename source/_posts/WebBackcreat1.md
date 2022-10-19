---
title: 在centos中部署后端程序。
date: 2022-10-16 21:19:32
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
5. nginx的启动和关闭
```bash bash
cd /usr/local/nginx/sbin
// 启动nginx
./nginx
// 关闭nginx
./nginx -s stop
// 关闭防火墙
systemctl stop firewalld
```
6. 创建目录
```bash bash
mkdir /var/temp/nginx -p
```
> 建议在执行前先使用netstat -aux | grep 80查看是否有端口占用80。

7. 开机自启 *还没写完
    1. 编辑文件
    ```bash bash
    cd /lib/systemd/system/
    vim nginx.service
    ```
    2. 添加内容
    ```bash bash
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
    ```bash bash
    systemctl start nginx.service
    systemctl enable nginx.service
    systemctl status nginx.service
    ```
8. 233
# Mysql 的安装
1. 先安装wget `yum install wget`。
2. 下载并安装mysql。
```bash bash
wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
yum -y localinstall mysql80-community-release-el7-7.noarch.rpm
yum -y install mysql-community-server
```
3. 启动mysql
```bash bash
// 启动mysql
systemctl start mysqld
// 写入开机启动
systemctl enable mysqld
```
4. 查找日志并找到临时密码。
```bash bash
vi /var/log/mysqld.log
```
> ![](/img/WebBackcreat1/20221019082932.png)  

5. 登录mysql
```bash bash
mysql -u root -p
// 密码则为查找到的临时密码。
password:
```

6. 修改密码
> 后面专门说这傻逼mysql改密码。

7. 重启mysql服务
```bash bash
service mysqld restart
```
> 重启后就可以正常使用mysql了。


