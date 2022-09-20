---
title: 在你的 linux nas 上安装一个种子下载器吧！
date: 2022-08-29 00:28:53
cover: /img/transmission/20220920213753.png
tags:
- transmission
- torrent
- 下载器
- nas
---
# Intro
Transmission 是一款流行的 BT 下载软件，比其他客户端使用更少的资源，守护程序非常适合服务器，并且可以通过桌面 GUI、Web 界面和终端程序进行远程控制，支持本地对等发现、完全加密、DHT、µTP、PEX 和 Magnet Link 等。
# 安装
这有啥好说的，直接 apt 一把梭：
```
sudo apt install transmission-daemon
```
不过在更改配置前，建议先关一下服务，以防万一：
```
sudo systemctl stop transmission-daemon
```

# 配置
1. transmission的配置文件在 `/etc/transmission-daemon/settings.json`
```
nano /etc/transmission-daemon/settings.json
```
2. 主要需要看看下面这几项：
```
"download-dir": "/home/pi/complete", # 下载目录 
"incomplete-dir": "/home/pi/incomplete", # 下载未完成文件目录
"rpc-authentication-required": false, # 懒得配置密码直接把验证关掉。
"rpc-host-whitelist-enabled": false,
"rpc-port": 9091, # 默认 Web 访问端口，这个可以根据需要修改一下。
"rpc-whitelist-enabled": false, # 白名单开关
"utp-enabled": true # 允许 Web 登录
```
3. 修改完成后启动daemon：
```
sudo systemctl start transmission-daemon
```
4. 浏览器打开 127.0.0.1:9091 查看效果：
![](/img/transmission/20220920213841.png)  

# 安装第三方主题
下载并执行TWC中文界面安装脚本
```
wget https://github.com/ronggang/transmission-web-control/raw/master/release/install-tr-control-cn.sh
bash install-tr-control-cn.sh
```
按 数字1 然后回车安装。
```
<< 2022-08-29 00:27:53 >> 安装脚本执行完成。如遇到问题请查看：https://github.com/ronggang/transmission-web-control/wiki
<< 2022-08-29 00:27:53 >> == 结束 ==
```
弹出如上提示则安装成功，返回web界面刷新即可。
![](/img/transmission/20220920213753.png)