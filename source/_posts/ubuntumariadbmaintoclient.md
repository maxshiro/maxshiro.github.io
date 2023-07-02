---
title: 在MariaDB中设置主从复制
categories: [linux,ubuntu]
date: 2023-05-23 11:21:40
tags:
- linux
- ubuntu
- mariadb
description:
cover: /img/ubuntumariadbmaintoclient/2023-06-12-23-19-26.png
banner: mariadb,ubuntu
---
本文将介绍如何在MariaDB中设置主从复制。主从复制是一种数据库复制技术，其中一个数据库服务器（称为“主服务器”）将其更改传递给一个或多个其他数据库服务器（称为“从服务器”）。主从复制可用于实现高可用性、负载均衡和数据备份等目的。

# 步骤

## 主机

1. 使用以下命令登录到MariaDB：

   ```bash
   mysql -uroot -p
   ```

2. 创建一个新的数据库并在其中创建一个新的表：

   ```mysql
   create database db1;
   use db1;
   create table test1(id int,name char);
   ```

3. 退出MariaDB并停止MariaDB服务器：

   ```mysql
   exit;
   systemctl stop mariadb
   ```

4. 使用编辑器打开MariaDB的配置文件。在CentOS中，配置文件位于 `/etc/my.cnf`，而在Ubuntu中，配置文件位于 `/etc/mysql/mariadb.conf.d/50-server.cnf`。

   ```bash
   vi /etc/my.cnf   # CentOS
   vi /etc/mysql/mariadb.conf.d/50-server.cnf   # Ubuntu
   ```

5. 在配置文件中添加以下内容：

   ```vim
   [mysqld]
   log-bin=/var/log/mysql/mysql-bin.log
   server-id=1
   binlog-do-db=db1
   bind-address=0.0.0.0
   ```

   上述配置文件中的关键配置包括：

   - `log-bin`：指定二进制日志文件的路径和名称。二进制日志文件包含主服务器上进行的所有更改。
   - `server-id`：指定主服务器的唯一标识符。每个服务器必须具有唯一的标识符。
   - `binlog-do-db`：指定要在二进制日志中记录更改的数据库名称。
   - `bind-address`：指定要在哪个IP地址上监听连接请求。在本例中，我们将其设置为 `0.0.0.0`，以允许从任何IP地址连接到主服务器。

6. 创建日志文件目录并将其所有权分配给MySQL用户：

   ```bash
   cd /var/log/
   mkdir mysql
   cd mysql/
   touch mysql-bin.log
   cd ../
   chown -R mysql:mysql ./mysql/
   ls -ld /var/log/mysql
   ls -ld /var/log/mysql/mysql-bin.log
   ```

   上述命令将创建一个名为 `mysql-bin.log` 的空日志文件，并将其所有权分配给 MySQL 用户。

7. 启动MariaDB服务器并重新登录到MariaDB：

   ```bash
   systemctl start mariadb
   mysql -uroot -p
   ```

8. 授予从服务器复制权限：

   ```mysql
   grant replication slave on *.* to slave@'%' identified by '123456';
   exit;
   ```

   上述命令将创建一个名为 `slave` 的用户，并为其授予复制权限。

9. 生成当前数据库的备份：

   ```bash
   systemctl restart mariadb
   mysqldump -uroot -p -A > all1.sql
   scp all1.sql 10.1.0.16:/root
   ```

   上述命令将在本地生成一个名为 `all1.sql` 的数据库备份，并将其复制到远程服务器 `10.1.0.16` 的 `/root` 目录中。

## 从机

1. 使用以下命令登录到从服务器：

   ```bash
   mysql -h 10.1.0.20 -uslave -p123456
   exit
   ```

2. 将备份文件还原到从服务器中的MariaDB：

   ```bash
   cd /root
   mysql -uroot -p < all1.sql
   mysql -uroot -p
   ```

3. 确认已成功复制数据库：

   ```mysql
   show databases;
   exit;
   ```

4. 停止从服务器上的MariaDB并编辑其配置文件：

   ```bash
   systemctl stop mariadb
   vi /etc/my.cnf   # CentOS
   vi /etc/mysql/mariadb.conf.d/50-server.cnf   # Ubuntu
   ```

5. 在配置文件中添加以下内容：

   ```vim
   [mysqld]
   server-id=2
   ```

   上述配置文件中的关键配置是 `server-id`，它指定从服务器的唯一标识符。

6. 启动从服务器上的MariaDB：

   ```bash
   systemctl start mariadb
   mysql -uroot -p123456
   ```

7. 在从服务器上配置主从复制：

   ```mysql
   change master to master_host='10.1.0.20',master_user='slave',master_password='123456',master_port=3306,master_log_file='mysql-bin.000003',master_log_pos=328,master_connect_retry=10;
   startslave;
   show slave status \G;
   ```

   上述命令将从服务器配置为将来自主服务器的更改复制到本地MariaDB上。`change master to` 命令中的参数包括：

   - `master_host`：指定主服务器的IP地址或主机名。
   - `master_user`：指定用于连接到主服务器的用户名。
   - `master_password`：指定用于连接到主服务器的密码。
   - `master_port`：指定主服务器的端口号。
   - `master_log_file`：指定主服务器当前正在使用的二进制日志文件的名称。
   - `master_log_pos`：指定主服务器当前正在使用的二进制日志文件中的位置。
   - `master_connect_retry`：指定从服务器在与主服务器断开连接后尝试重新连接的次数。

   ``start slave;` 命令将从服务器上的复制进程启动。`show slave status \G;` 命令将显示有关从服务器上的复制进程的详细信息。

   至此，主从复制已经设置完成。主服务器上的更改将自动传播到从服务器上。