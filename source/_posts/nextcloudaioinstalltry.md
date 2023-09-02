---
title: 用家宽搭建Nextcloud-aio的一种方法
categories: [nextcloud]
date: 2023-09-02 23:41:18
tags:
- nextcloud
- docker
- linux
- NPM
cover: /img/nextcloudaioinstalltry/2023-09-03-00-07-35.png
---
# 引言
出于学习目的对这玩意进行了一通研究，我的评价是如果你没有80和443端口那我建议你直接跳过或者重开，没必要那这玩意来搞生产。除非你出于学习目的。
如果你的公网地址可以访问80和443端口的话你可以参考这个教程：[【好玩儿的 Docker 项目】Nextcloud All-in-One 全新搭建分享，拒绝繁琐配置，开箱即用！维护简单！](https://blog.laoda.de/archives/docker-compose-install-nextcloud-aio/)
## 项目特点
- 支持 docker 一键部署，用容器部署容器
- 更新简单
- 高性能后端
- 支持全局搜索
- 支持 Nextcloud Talk
- 支持 ClamAV（Nextcloud 的杀毒软件后端）
- 支持全套办公套件
- 支持备份（基于 BorgBackup）
- 省心省力
- 文档及其丰富（各种地方的搭建教程均有）（个屁）
- ……
## 项目展示
![](/img/nextcloudaioinstalltry/2023-09-03-01-12-40.png)
![](/img/nextcloudaioinstalltry/2023-09-03-01-13-03.png)
![](/img/nextcloudaioinstalltry/2023-09-03-01-13-19.png)
## 本文引用链接
[GitHub 官方仓库](https://github.com/nextcloud/all-in-one)  
[官方 Docker 镜像](https://hub.docker.com/r/nextcloud/all-in-one)  
[官方反代相关文档地址](https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md) （支持各种反代）
[【好玩儿的 Docker 项目】Nextcloud All-in-One 全新搭建分享，拒绝繁琐配置，开箱即用！维护简单！](https://blog.laoda.de/archives/docker-compose-install-nextcloud-aio/)
[cloudflare证书的导出（crt+key格式）](https://www.bilibili.com/read/cv8513826/)
[利用cloudflare免端口号访问家庭自建服务，可添加十条记录，去掉端口号！](https://www.bilibili.com/video/av777707955/)
[# 通过 Origin Rules 使 CloudFlare CDN 回源以使用任意的端口](https://blog.misaka.rest/2023/01/16/cf-any-port/)

# 搭建前置
- 拥有一台性能足够的linux服务器
- 已经在路由器上配置好了ddns解析
- 创建好cloudflare账号
- 安装好docker
- 安装好NPM（Nginx Proxy Manager）（这里我教一下这个吧）
# 安装NPM
## 什么是NPM？
NPM（Nginx proxy manager）是一个很简单的反向代理工具。

官网：[https://nginxproxymanager.com/](https://nginxproxymanager.com/)

门槛极低，操作简单，不需要你掌握很复杂的 Nginx 配置知识，只需要几步就能很轻松完成反向代理的设置和 SSL 证书的部署。
## 安装
创建目录：
```bash
// 切换到root用户
sudo -i
// 创建安装目录并进入
mkdir -p ~/data/docker/npm && cd ~/data/docker/npm
// 编辑配置文件
nvim docker-compose.yml
```
添加下面的内容：
```yml
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '8880:80' # 这三行根据自己情况改，前面对应你本机地址，8881即为后面要访问的地址。
      - '8881:81'
      - '8443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```
安装：
```bash
docker compose up -d
```
放行端口：
```bash
firewall-cmd --add-port=8880/tcp --permanent
firewall-cmd --add-port=8881/tcp --permanent
firewall-cmd --add-port=8443/tcp --permanent
```

之后就可以进入`http://ip:8881`来访问了
默认的登录名和密码：
```bash
Email:    admin@example.com  
Password: changeme
```
## 更新
```bash
cd ~/data/docker/npm  
  
docker-compose down   
  
cp -r ~/data/docker/npm ~/data/docker/npm.archive  # 万事先备份，以防万一  
  
docker-compose pull  
  
docker-compose up -d    # 请不要使用 docker-compose stop 来停止容器，因为这么做需要额外的时间等待容器停止；docker-compose up -d 直接升级容器时会自动停止并立刻重建新的容器，完全没有必要浪费那些时间。  
  
docker image prune  # prune 命令用来删除不再使用的 docker 对象。删除所有未被 tag 标记和未被容器使用的镜像
```
## 卸载
```bash
cd ~/data/docker/npm 
  
docker-compose down   
  
rm -rf ~/data/docker/npm  # 完全删除映射到本地的数据
```

# 安装本体
创建目录：
```bash
// 切换到root用户
sudo -i
// 创建安装目录并进入
mkdir -p ~/data/docker/naio && cd ~/data/docker/naio
// 编辑配置文件
nvim docker-compose.yml
```
添加如下内容：
```yml
version: "3.8"

volumes:
 nextcloud_aio_mastercontainer:
   name: nextcloud_aio_mastercontainer
services:
 nextcloud:
   image: nextcloud/all-in-one:latest
   restart: unless-stopped
   container_name: nextcloud-aio-mastercontainer
   volumes:
     - nextcloud_aio_mastercontainer:/mnt/docker-aio-config
     - /var/run/docker.sock:/var/run/docker.sock:ro
   ports:
     - 8890:8080 # change the port on the left side if it's already in use on your host system.
   environment:
     - APACHE_PORT=11000  # change this port number if 11000 is already in use on your host system.
     - APACHE_DISABLE_REWRITE_IP=1
     - APACHE_IP_BINDING=0.0.0.0
#     - NEXTCLOUD_TRUSTED_DOMAINS=pan.gugu.ovh 31.22.109.18 # Your domain name + proxy host IP
#     - TRUSTED_PROXIES=31.22.109.18 # proxy host IP
```
安装：
```bash
docker compose up -d
```
放行端口：
```bash
firewall-cmd --add-port=8890/tcp --permanent
firewall-cmd --add-port=11000/tcp --permanent # 请在你的路由器内创建端口映射把这个端口映射到公网。
```
# 配置反代
打开cloudflare，选择你添加好的域名，创建一条CNAME记录，指向你已经在路由器内配置好的ddns域名（这里使用的是`nas.xxx.net`你要改成你自己的）：
![](/img/nextcloudaioinstalltry/2023-09-03-01-13-52.png)
点击保存。

打开你的NPM控制面板，选择上面的`SSL Certificates`，点击`Add SSL Certificate`-`Custom`我们添加一个证书（原文这里用的是让老子加密，这里咱们可以试试cloudflare的免费证书，足足有**15**年！！！）：
![](/img/nextcloudaioinstalltry/2023-09-03-01-14-36.png)
现在打开你的cloudflare控制面板，右边选择SSL/TLS，我们先把这个改成灵活：
![](/img/nextcloudaioinstalltry/2023-09-03-01-15-04.png)
然后到侧边栏选择源服务器，创建一个泛域名证书，只用更改证书有效期，改成15年
新建一个xxx.key文件，把key里面的写进去。
新建一个xxx.pem文件，把另一串粘进去。
然后返回到你的NPM里面添加证书：
![](/img/nextcloudaioinstalltry/2023-09-03-01-18-26.png)
然后选择Hosts-Proxy Hosts：
![](/img/nextcloudaioinstalltry/2023-09-03-01-18-45.png)
Details选项卡里：
![](/img/nextcloudaioinstalltry/2023-09-03-01-19-02.png)
SSL选项卡里选择你创建的证书，剩下全部勾上：
![](/img/nextcloudaioinstalltry/2023-09-03-01-19-17.png)
Advanced选项卡里填入下面的内容：
```bash
client_body_buffer_size 512k;  
proxy_read_timeout 86400s;  
client_max_body_size 0;
```
然后保存

**IP 填写：**
如果 Nginx Proxy Manager 和 aio-nextcloud 在同一台服务器上，可以在终端输入：
```bash
ip addr show docker0
root@nasdb:~/data/docker_data/naio# ip addr show docker0
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:4a:dd:c1:14 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:4aff:fedd:c114/64 scope link
       valid_lft forever preferred_lft forever
```
否则直接填 `aio-nextcloud` 所在的服务器 IP 就行。

# 配置回源
返回到cloudflare，侧边栏里选择规则-Origin Rules：
![](/img/nextcloudaioinstalltry/2023-09-03-01-19-42.png)
添加一条规则：
![](/img/nextcloudaioinstalltry/2023-09-03-01-19-56.png)

如果你看不到图：
当传入请求匹配时...

|字段|运算符|值||
|-|-|-|-|
|主机名|等于|nas.xxx.net|and|
|and||||
|SSL/HTTPS|等于|勾选||
目标端口：重写到`11000`
然后点部署

# 配置aio
打开`https://yourip:8890`然后记下密码点击下面的按钮。剩下的就和普通的没啥差别了。

# 卸载
和卸载npm一样。

