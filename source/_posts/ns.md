---
title: 使用yuzu模拟器玩动森 ( 附动森资源 )
date: 2022-08-28 21:12:24
sticky: true
cover: img/ns/20220920215433.png
tags:
- switch
- Project Diva
- Miku
- Mega39
- emulater
---
![](/img/ns/1.gif)
# 引子
> 好久没有关注过 switch 模拟器了.这几天突然想玩歌姬计划,想起来之前找到个 yuzu 模拟器,正好想试试看能不能跑 MEGA39 ,便有了下文
# 配置要求
## 软体:
> 建议运行 Windows 10 1803 或较新版本以获得最佳性能.这里仅列出 Win10 64位 要求.

---

> 硬体要求适用于所有 Switch 游戏.
## CPU:
> 任何支持 FMA 指令集的 x86_64 CPU。 建议使用 6 个或更多线程。

* 最小: Intel Core i5-4430 / AMD Ryzen 3 1200
* 推荐: Intel Core i5-10400 / AMD Ryzen 5 3600

## 专用显示卡:
**必须使用 OpenGL 4.6 或 Vulkan 1.1 兼容硬件和驱动程序。 推荐使用支持半浮点以及 4GB 显示内存。**

> 大多数游戏都可以在 Fermi 系列（400 系列）或更高版本的 Nvidia GPU 上运行，但强烈建议至少使用 Pascal（1000 系列）。

* 最小: NVIDIA GeForce GT 1030 2GB / AMD Radeon RX 550 2GB
* 推荐: NVIDIA GeForce GTX 1650 4GB / AMD Radeon RX Vega 56 8GB

## 集成显示卡:
> 集成显卡的性能表现很差。 独立显卡能够在所有场景中产生更好的效果。 这里列出 iGPU 支持。

* 最小: Intel HD Graphics 520 / AMD Radeon Vega 3
* 推荐: Intel UHD Graphics 750 / AMD Radeon Vega 7

## 内存:
> 因为集成 GPU 调用内存作为显存 (VRAM)，所以在此配置中的内存要求要高些。

* 最小专用显卡内存: 8GB
* 最小集成显卡内存: 12GB
* 推荐: 16GB

# 下载安装本体
> 下列操作建议在科学上网环境下进行.

1. 去 <https://yuzu-emu.org/> 下载最新的 yuzu 模拟器. 并运行软体.
   > 我在下方的链接里也放了捐赠版也就是EA，但是版本不会经常更新。如果安装出现问题可以试试。
   > yuzu EA版本还支持多人联机的功能，但是需要你使用梯子才可以比较好的使用。
  * yuzu Early Access 这个版本是只有捐赠用户才可以使用, 如果条件支持可以去支持一波.
  * 普通版本已经足够使用.

2. 由于安装的时候会连接 Github 服务器下载文件, 因此强烈建议使用全局科学上网.
3. 安装完成后直接启动, 提示缺少 key 文件, 选 OK :
> ![](/img/ns/20220920215251.png)  

1. 然后会跳出提示框问是否帮助改进 yuzu , 我们点 no.

2. 此时已经完成安装了模拟器本体, 不过想要正常运行游戏还需要做以下操作
3. 执行工具栏中的 `文件` -> `Open yuzu Folder` 打开 yuzu 软件自动创建的目录, 然后在下方的链接里找到key文件夹，下载 `prod.keys` 文件扔进 keys 文件夹内.
4. 然后再次打开 yuzu 就不会再弹出刚才的对话框了.

# 模拟器设置
执行 `模拟(E)` -> `Configure...` 打开 yuzu 设置.  
## 通用选单
* Interface 下第一项（interface language）可以更改显示语言，建议先把这个改为中文。
* 勾选 `多核 CPU 仿真` 可在一定程度上改进性能.
* `运行速度限制` 更改到 100% 可解决某些游戏没有帧数限制导致游戏速度异常。
* 网络选单下可以填写你注册yuzu网站后的令牌以此来进行联机。
## 系统选单
* 在 系统 选单, 系统 选项卡, 系统设置 框架内根据需要设置 `语言`, `地区` 和 `时区` ,这里推荐 `中文`, `中国`, `中文` 不然默认语言看不懂 (
* 配置选项卡可以更改个人信息但是没什么用。
* 网络选项卡若要使用联机服务则需要选择一种连接方式，通常是以太网或者WLAN。这取决于你当前使用什么方式上网。
## 图形选单
* `API` 任意选择，N卡选Vulkan有时候也更好。
* 如果你的设备是笔记本，那你需要更改 `设备` 为自己的独立显卡。
## 控制选单
* `输入设备` 内可以选择自己的手柄/控制器。唯一需要注意的是switch按键的ab/xy按键是反过来的，建议游玩的时候别看手柄。
* 最后点击 `OK` 即可完成配置...

# 下载游戏
> 我发现[Switch520](https://xxxxx520.com/ "Switch520")这里的游戏下载没那么多门槛要会员什么什么的, 不过都是百度网盘, 建议整一个百度网盘会员.(现在有插件可以实现免会员高速下载，有空我再写写:P)

文中提到的文件可以到这个链接：**[Sharepoint](https://maxshiro-my.sharepoint.com/:f:/g/personal/maxshiroi_maxshiro_onmicrosoft_com/El4X0CVz-TBKqO0k_-_BWRcBt4uT7Tf-gVJtrKdPhbyP5A?e=IUlqdf "Sharepoint")** 里面下载获取。

下载后解压拿到 xxx.xci 文件, 这就是游戏本体了. 将这个游戏本体放到任意文件夹内 (不要有中文) . 双击 yuzu 模拟器中间, 选择你放 xci 文件的那个文件夹打开, 然后你就会发现游戏已经加载出来了. 双击打开爽玩吧~