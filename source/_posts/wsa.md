---
title: Win11 安装 WSA 简易教程
date: 2022-08-28 20:59:44
cover: http://axro.top:85/i/2022/08/28/630b737e7e3ee.png
tags:
- Android
- Hyper-V
- WSA
---
> 微软终于在昨天上线了WSA的测试版本,此次只有美国地区上线了.因此如果你的Win11系统为美区的话,可以直接点击 [这个链接](https://www.microsoft.com/store/productId/9P3395VX91NR "这个链接") 进行安装.如果非美区那就接着看步骤吧

> bug 还是有很多, 首先是 WSA 无法调用GPU, 这就导致很多 3d 游戏会很卡, 实测明日方舟帧数不到 20 帧. 然后安装WSA要启用 Hyper-V 即微软的虚拟机平台, 打开这个会导致无法再安装和使用其他安卓模拟器. 和市面上大多数安卓模拟器相比, 微软这个 WSA 是 ARM 翻译运行的, 安卓版本更高 (Android 11), 可以运行 ARM 程序. 与系统集成度更好 (虽然目前还没有实现 WSL 那样的文件访问) 而模拟器大多都为 Android x86 项目改的, 因此最高只能到 Android 9.

> 如果你是想尝尝鲜, 试试这个新功能那我还是推荐试一试. 如果你主要想拿来打游戏, 日常使用的话那还是更推荐安卓模拟器一些.

# 开启虚拟化

> PS: 如果之前你有禁用过 Hyper-V 的话记得使用管理员权限打开 Powershell 然后通过这个指令这个指令重新打开, 不记得有没有那就跳过:
```
bcdedit /set hypervisorlaunchtype auto
```

1. 打开开始菜单,直接搜索控制面板,然后打开.
2. 找到 `程序和功能`-`启用或关闭 Windows 功能` 并点击打开, 会弹出一个窗口.
3. 在弹出窗口里面勾选 `Hyper-V`、`Windows 虚拟机监控程序平台`、`适用于 Linux 的 Windows 子系统`、`虚拟机平台` 这几项, 然后点击确定. 系统会自动查找更新并安装.
4. 等待重启过后就可以进行下一步了.

# 安装本体

1. 首先复制上面那个链接.
2. [安装包抓包](https://store.rg-adguard.net/ "安装包抓包"),点开这个网址, 将第一步复制的链接粘贴到搜索框,右边选择 `slow` 然后点对勾.
3. 找到最下面名为 `MicrosoftCorporationII.WindowsSubsystemForAndroid_1.7.32815.0_neutral_~_8wekyb3d8bbwe.msixbundle` 的一项, 注意大小为 `1.21GB` , 直接点击进行一个下载.
4. 找到你下载的文件, 然后右键 `开始按钮` 选择 `Windows 终端 (管理员)`
5. 然后会打开 Powershell 窗口, 在里面输入指令
```
add-appxpackage "你下载文件的完整路径" 
# example
add-appxpackage "D:\download\MicrosoftCorporationII.WindowsSubsystemForAndroid_1.7.32815.0_neutral_~_8wekyb3d8bbwe.msixbundle"
```
6. 等他跑完以后就会在开始菜单里出现 `Windows Subsystem for Android™` 点击打开是下面这个界面就说明安装成功了.
[![233](https://axro.top/pic/001.png "233")](https://axro.top/pic/001.png "233")

# 安装apk
1. 点击文件后面的按钮会自动启动 WSA, 等打开后下面的 ip 地址会刷新. 此时我们将开发人员模式打开.
2. 安装 ADB, 下载 [这个文件](https://nas.axro.top/index.php/s/frYxCHg7FfabNEY "这个文件") 然后解压到任意目录, 然后打开解压出来的文件夹, 名称为 `platform-tools`.
3. 按住`Shift`键右击文件夹的空白处, 选择`显示更多选项`-`在此处打开 Powershell 窗口(s)`, 然后在打开的窗口内依次输入以下指令:
```
adb connect 127.0.0.1:58526 #这个 127.0.0.1:58526 为你的开发人员选项那一栏里显示的地址.
# 第一次可能会失败, 再输入一次就可以了.
adb install -r xxx.apk
# 这个指令为安装 apk, 将 xxx 替换为你要安装的 apk 文件名称.
```
4. 显示安装完成就会出现在你的开始菜单里, 然后直接点击进行一个打开. 

# Q&A