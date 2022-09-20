---
title: 使用树莓派4B+Docker Openwrt实现旁路由
date: 2022-04-26 20:56:45
updated: 2022-08-23 20:56:45
cover: img/openwrt1/20220920215607.png
tags: 
  - 树莓派
  - docker
  - openwrt
  - router
---
# 前言
> 普通路由器往往受限于硬体性能而缺乏很多功能.但是其拥有多网口和无线功能的支持相对较强.为了能实现更多实用功能并且利用上已经吃灰了很久的树莓派,打算用树莓派来当作旁路由.
# 硬体
## 路由
* 我使用的是小米路由3G(下面称为`R3G`).当时购买的是小米路由4版本,但是其缺乏很多功能.就比如说非常实用的usb接口.有了usb接口以后就可以愉快刷机了.所以我收到货就直接退货换了R3G...
* R3G优点是有一个usb3.0的接口,所以可以直接拿它来挂载移动硬盘也是没问题的.还有256M的闪存.缺点是只有两个RJ45LAN口,一个RJ45WAN口足够家用.所以后来我又白嫖了我哥在外地买的一台TPlink五口交换机解决了网口不够的问题...
* 如果你和我一样拥有R3G.我建议刷写不死鸟breed搭配[hiboyhiboyhiboy](https://opt.cn2qq.com/padavan/ "hiboyhiboyhiboy")的固件,来实现更多功能...

## 树莓派
* 树莓派4B我购买的是4G内存版本.采用5V3A,Type-C供电接口,你甚至可以用一个充电宝和一根Type-C线来给树莓派供电.两个MicroHDMI接口支持4K视频输出.一个极为先进的3.5mm音频接口.四个Type-A USB接口,其中分为两个usb2.0协议,两个USB3.0协议.一个千兆RJ45网口.另外机器还自带wifi模块,就是别指望这个wifi信号有多好了...
* 我在购买时还一并购买了一张32G闪迪的SD卡,以及适用于4B的九层亚克力外壳.有一说一,这个外壳属实好看...
* 不过如果拿它刷openwrt只来当作路由器实在大材小用.所以我采用OPENFANS和[树莓派爱好者基地](http://www.pifan.org.cn/ "树莓派爱好者基地")联合编译的[Debian-Pi-Aarch64](https://github.com/openfans-community-offical/Debian-Pi-Aarch64 "Debian-Pi-Aarch64")来使用

# 软体
## 准备
* 从上方给出的链接下载[Debian-Pi-Aarch64](https://github.com/openfans-community-offical/Debian-Pi-Aarch64 "Debian-Pi-Aarch64"),我这里使用的是没有图形界面的增强版,即`2021-06-11-OPENFANS-Debian-Buster-Aarch64-ext4-v2021-2.0-U6-Release.img.xz`
* 百度搜索Rufus,下载到本地

## 安装 Debian-Pi-Aarch64
1. 将SD卡接入读卡器连接电脑.注意提前备份SD卡的数据,接下来的操作会清除SD卡内的全部数据.
2. 打开Rufus软体,在`设备`一栏内选择你的SD卡,在`引导类型选择`栏里面单击后面的`选择`按钮,选择你下载的xz文件
3. 单击下面的`开始`按钮即可开始刷写.大概三四分钟后即可刷写成功.请耐心等待...
4. 刷写完成后在任务栏上将SD卡弹出,即可拔下读卡器.将SD卡插入树莓派里面即可.
5. 将树莓派的LAN口与路由器的LAN口连接,为树莓派接入电源.此时树莓派会自行重启三次左右,以完成基本配置.此时可以喝杯咖啡等待三分钟左右.
6. 电脑登入路由器控制页面,查找树莓派的ip,我这里查到的是`192.168.0.90`,接下来的教程将以这个地址为准...
7. 如果您使用的是Win10系统,那么可以直接按下`Windows徽标键`+`R`,输入`CMD`指令单击回车来打开`CMD`,使用`SSH`的方式连入树莓派.当然你也可以使用其他第三方软件来实现.
8. 使用`ssh -p 22 pi@192.168.0.90`指令来接入树莓派,如果没有出错的话接下来会弹出提示,输入`yes`并按下回车即可.当你看见password时,直接输入默认密码`raspberry`,按下回车即可连入树莓派.
9. 更新一下就可以正常使用了
```
sudo apt-get update && sudo apt-get upgrade
```
## 配置 Debian-Pi-Aarch64

**如果自己会配置那么这步可以跳过了**

> 可以参考[这里](https://gitee.com/openfans-community/Debian-Pi-Aarch64#3%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E "这里")来配置

### 切换账户
`su`或者`sudo -i`
### 更改账户的密码
```
// 使用下面指令来更改pi用户的密码
// 重复输入两次即可
sudo passwd pi

// 使用这个指令来更改root账户的密码
sudo passwd root
```
### 挂载smb硬盘
```
cd /
mkdir db
mount -o username=xxx,password=xxx,dir_mode=0777,file_mode=0777 //192.168.0.15/database /db
```
## 安装 Openwrt
> 在安装之前首先开启docker服务并设置开机自动启动

* 启用和运行Docker服务
```
// 开机自动启动Docker服务
systemctl enable docker.service

// 启动Docker服务
systemctl start docker.service

######

// 停止Docker服务
systemctl stop docker.service

// 禁止Docker服务开机启动
systemctl disable docker.service
```
1. 打开网卡混杂模式
```
sudo ip link set eth0 promisc on
```
2. 创建网络
```
docker network create -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 macnet 
```
> 使用`sudo ifconfig`命令查看树莓派`eth0`网卡的参数来更改
> `--subnet`参数改为树莓派所处网段
> `--gateway`参数改为树莓派所处网段的网关

* 使用`docker network ls`查看创建好的网络

3. 拉取Openwrt镜像
```
docker pull registry.cn-shanghai.aliyuncs.com/suling/openwrt:latest 
```

* 执行`docker images`命令查看现存镜像

4. 创建并启动容器
```
docker run --restart always --name openwrt -d --network macnet --privileged registry.cn-shanghai.aliyuncs.com/suling/openwrt:latest /sbin/init 
```
> `--restart always`参数表示容器退出时始终重启，使服务尽量保持始终可用；
> `--name openwrt`参数定义了容器的名称；
> `-d`参数定义使容器运行在`Daemon`模式；
> `--network macnet`参数定义将容器加入`maxnet`网络；
> `--privileged`参数定义容器运行在特权模式下；
> `registry.cn-shanghai.aliyuncs.com/suling/openwrt:latest`为 Docker 镜像名，因容器托管在阿里云 Docker 镜像仓库内，所以在镜像名中含有阿里云仓库信息；
> `/sbin/init`定义容器启动后执行的命令。

* 可以使用`docker ps -a`命令查看当前运行的容器
> 若容器运行信息`STATUS`列为`UP`状态，则说明容器运行正常。

5. 进入容器并修改相关参数
```
docker exec -it openwrt bash
```
* 编辑`OpenWrt`的网络配置文件：
```
nano /etc/config/network
```
* 更改LAN口设置
```
config interface 'lan'
        option type 'bridge'
        option ifname 'eth0'
        option proto 'static'
        option ipaddr '192.168.0.2'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option gateway '192.168.0.1'
        option broadcast '192.168.0.255'
        option dns '192.168.0.1'
```
> `option ipaddr`为 OpenWrt 的 IP 地址,可以根据自己喜好修改,我这里定义为了`192.168.0.2`

* 重启网络
```
/etc/init.d/network restart
```

6. 进入控制面板
> 打开电脑的浏览器,使用自己定义的地址进入.我这里是`192.168.0.2`
> 用户名:`root`
> 密码:`password`

7. 关闭 DHCP 服务
> 在 "网络 - 接口 - Lan - 修改" 界面中,勾选下方的"忽略此接口(不在此接口提供 DHCP 服务)",并"保存&应用".

## 修复宿主机网络
OpenWrt 容器运行后,宿主机内可能无法正常连接外部网络,需要修改宿主机的`/etc/network/interfaces`文件以修复：
1. 备份并编辑文件
```
cp /etc/network/interfaces /etc/network/interfaces.bak # 备份文件
nano /etc/network/interfaces # 编辑文件
```
2. 向文件末尾添加
```
auto eth0
iface eth0 inet manual

auto macvlan
iface macvlan inet static
  address 192.168.0.90
  netmask 255.255.255.0
  gateway 192.168.0.1
  dns-nameservers 192.168.0.1
  pre-up ip link add macvlan link eth0 type macvlan mode bridge
  post-down ip link del macvlan link eth0 type macvlan mode bridge
```
* 修改后重启树莓派,之后树莓派的局域网 IP 地址将会固定为`/etc/network/interfaces`文件中`address`参数中的地址.

# 鸣谢(摘抄)
[在Docker 中运行 OpenWrt 旁路网关](https://mlapp.cn/376.html "在Docker 中运行 OpenWrt 旁路网关")


