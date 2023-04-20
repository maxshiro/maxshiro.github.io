---
title: 在Debian系linux上通过apt的方式安装plex媒体中心
date: 2022-11-02 20:44:17
img: img/linuxplexguide/20221102204637.png
tags:
- media center
- plex
- emby
- jellyfin
- 视频
- 音乐
---
# Intro
plex是一个包含订阅制的个人媒体中心，可以在你的智能设备：Phone / Android TV / Windows / MAC OS 等在线使用专用的客户端来浏览你存储在nas上的影片。
![](/img/linuxplexguide/20221102204637.png)  
如果你付费订阅则会支持更多的功能如在线硬解码/使用dashboard来控制设备/使用plexamp来播放音乐。当然免费的功能已经足够我们使用。
# 安装
1. 首先更新
```bash
sudo apt update && sudo apt upgrade
```
2. 安装apt-transport-https包
> 要将Plex包安装到树莓派上，需要添加官方的Plex包仓库。这个包允许 “apt” 包管理器通过Plex仓库使用的 “https” 协议来检索包。
```bash
sudo apt install apt-transport-https
```
3. 把Plex仓库添加到apt源
```bash
curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
```
4. 再次更新
```bash
sudo apt update
```
5. 一行命令安装Plex
```bash
sudo apt install plexmediaserver
```

# 使用Plex
打开 `Your IP:32400/web` 来登录并配置你自己的Plex服务器

## 在安卓设备上安装plex和plexamp软件。
> 不保证时效性，若失效请自行查找。
* plex 软件开心版[点我下载](https://apkmody.io/apps/plex)
![](/img/linuxplexguide/image.png.png)  
* plexamp 软件开心版[点我下载](https://forum.mobilism.me/viewtopic.php?f=1332&t=4970093)
![](/img/linuxplexguide/20221102210820.png)  
* symfonium 这个没开心版，但是界面好看。
![](/img/linuxplexguide/20221102210737.png) 
![](/img/linuxplexguide/20221102210803.png)  

## 安装Wangyiyun插件来自动匹配音乐封面和歌词。
1. 使用 git clone 下载。
```bash
sudo git clone https://github.com/timmy0209/WangYiYun.bundle.git /usr/lib/plexmediaserver/Resources/Plug-ins-f4cdfea9c/WangYiYun.bundle
```
2. 重启plex
```bash
systemctl restart plexmediaserver
```
3. 直接在音乐源里勾选wangyiyun即可。
