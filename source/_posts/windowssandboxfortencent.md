---
title: 使用Windows内置的沙盒功能来隔离常见的国产软件
categories: [Windwos]
date: 2023-07-25 10:38:02
tags:
- windows
- qq
- sandbox
- tencent
hide: false
cover: /img/windowssandboxfortencent/2023-07-25-11-05-29.png
---
# 使用本项目的目的
![](/img/windowssandboxfortencent/2023-07-25-11-05-29.png)
腾讯系的软件众所周知在使用中会有个进程随着主进程的启动一同启动。这个程序会读取本地的浏览器浏览记录以及一些其他的个人隐私信息并上传到腾讯那边进行分析。具体他拿来做什么并不是很清楚，但是有一些猜测：
1. 反作弊相关
2. 精确探测用户画像实现精准推送
3. 与政府联合进行某些行动。

这些年大火一直在搞如何才能防止腾讯系软件对用户信息的收集。目前的方案有如下几种：
1. 使用VMware/vbox之类的虚拟机软件来安置这些毒瘤，缺点：占用空间和性能都很多，而且启动很麻烦。
2. 使用sandboxie这类沙箱软件，缺点：没有plus版本会缺失很多功能。
3. 使用另外的机器来安置这些软件，缺点：并不是所有人都那么rich。
4. 使用windows沙盒：

windows沙盒在正常使用的时候和普通机器并无太大区别，但是当你关闭windows沙盒之后里面的内容也会随之消失。

因此本项目的目的就是提供一种新的解决方案，在本地创建一个文件夹用来保存windows沙盒产生的一些文件，从而可以不用每次启动都需要重新安装这些软件。但并不能照顾到所有的文件，所以还是会有一些缺陷。

# 注意

**本项目由`empty-233/tencent-sandbox`克隆而来，为自己方便使用。**

# tencent-sandbox

tencent-sandbox 是使用 [Windows sandbox](https://learn.microsoft.com/zh-cn/windows/security/application-security/application-isolation/windows-sandbox/windows-sandbox-overview) 为腾讯全家桶创建沙盒的配置

# Chinaflavor-sandbox

[Chinaflavor-sandbox](https://github.com/maxshiro/Chinaflavor-sandbox) 是由`tencent-sandbox`个人定制而来，添加了腾讯文档，腾讯会议以及钉钉。

## 注意事项

**Windows 10 不支持相对路径**，请把 **Tencent.wsb** 中的路径替换成**绝对路径**

## 兼容性

目前测试 **微信** **QQ** **QQNT** **TIM** **企业微信** 正常使用，其他未测试

注: **企业微信无法自动登录(检查设备)，只能每次重新登录**

默认分配**2gb**内存，有需要可以自行修改 `<MemoryInMB>2048</MemoryInMB>`

## 使用教程

### 如何启用windows沙盒？

**注意：请确保您的Windows系统在Win10以上，以及Windows沙盒功能不适用于Windows 10/11 家庭版。您可以使用`winver`命令来检查系统版本。**

1. 按下`Win + R`键，打开“运行”对话框，然后输入`control`并按`Enter`键，这将打开控制面板。
2. 在控制面板中，将查看到若干个选项类别。请找到并点击“程序”菜单。
3. 在“程序”菜单下，您将看到一个名为“启用或关闭Windows功能”的链接。请点击它，这将打开一个名为“Windows功能”的窗口。
4. 在“Windows功能”窗口中，您将看到一个列表，其中包含了可启用或禁用的Windows功能。请在列表中找到“Windows沙盒”选项，并确保其复选框被勾选上。
5. 在勾选了“Windows沙盒”选项后，点击“确定”按钮。Windows将开始应用更改，这可能需要一些时间。完成后，您将看到一个提示，要求您重启计算机，以使更改生效。请点击“是”，让系统重启。
6. 当您的计算机重新启动后，按下`Win + S`键，打开Windows搜索功能。在搜索栏中输入“Windows沙盒”，然后点击出现的“Windows沙盒”应用程序。
7. 点击“Windows沙盒”应用程序后，Windows沙盒将开始初始化。稍等片刻，您将看到一个新的桌面环境弹出，类似于一个干净的Windows桌面。这表示Windows沙盒已经成功启动。

### Clone本项目到任意目录

``` bash
git clone https://github.com/maxshiro/Chinaflavor-sandbox.git
```

### 创建空文件夹目录结构

双击根目录中的`mkdir.bat`一键创建以下目录结构。

``` tree
├───App
│   ├───DingDing
│   ├───Docs
│   ├───Meeting
│   ├───QQ
│   ├───QQNT
│   ├───TIM
│   ├───WeChat
│   └───WXWork
├───Data
│   ├───Common Files
│   ├───Documents
│   │   ├───Tencent
│   │   ├───WeChat
│   │   └───WXWork
│   ├───Roaming
│   │   ├───DingTalk
│   │   ├───Tencent
│   │   ├───WeChat
│   │   └───WXWork
│   └───SysWOW64
├───Desktop
└───Scripts
```

### 安装配置沙盒

1. 在沙盒内正确安装需要的软件，正常安装 **QQ/QQNT/Wechat/Dingtalk**。

2. 安装完成之后复制沙盒内的 `C:\Windows\SysWOW64` 目录下所有文件到Clone下来的 `Data\SysWOW64`目录内 (不然qq运行一段时间后会崩溃)，并关闭沙盒。

3. 打开 **Tencent.wsb**，继续安装列出来的软件。
4. 移动桌面快捷方式到任何位置，再移动回桌面(否则快捷方式会消失)。

操作完成后就可以正常使用了，不确保账号数据会保留。

### 自定义

如果你想要自定义添加的软件，可以参考我提供的思路：

1. 沙盒内正常安装你需要的软件到默认目录下（随便）,然后复制这个目录的路径。

2. 可以到下面的几个目录内看看你装的软件是否创建了其他目录：

   ```dir
   C:\Users\WDAGUtilityAccount\Documents\
   C:\Users\WDAGUtilityAccount\AppData\Roaming\
   C:\Program Files (x86)\Common Files
   ```

3. 使用任意文本编辑器打开 **Tencent.wsb** 这个文件就能看到这个沙盒是如何工作的。（大概意思就是把配置过的目录映射到虚拟机里）

4. 找到对应的`<!-- Common Files -->`,`<!-- App -->`,`<!-- Documents -->`,`<!-- Common Files -->`,`<!-- ProgramData -->`注释标签，在对应位是添加你要映射的目录：

   ```
   # 以钉钉为例：
   <!-- App -->
   # 在这个标签下添加这些内容
   <MappedFolder>
   	<HostFolder>.\App\DingDing</HostFolder> # HostFolder 本机位置
   	<SandboxFolder>C:\Program Files (x86)\DingDing</SandboxFolder> # SandboxFolder 沙箱内位置
   	<ReadOnly>false</ReadOnly> # ReadOnly 是否只读
   </MappedFolder>
   
   <!-- Roaming -->
   <MappedFolder>
   	<HostFolder>.\Data\Roaming\DingTalk</HostFolder>
   	<SandboxFolder>C:\Users\WDAGUtilityAccount\AppData\Roaming\DingTalk</SandboxFolder>
   	<ReadOnly>false</ReadOnly>
   </MappedFolder>
   ```

5. 在主机Data目录下创建上面HostFolder编写的目录位置。

6. 关闭虚拟机，重新打开并再次正常安装（注意安装位置要和你自己上面填的目录保持一致），按照第一次的方式再次拷贝快捷方式并恢复。

7. 其余的参考文件内的内容自行修改。

## 挂载路径

``` text
桌面 -> Desktop
C:\Program Files (x86)\Common Files -> Data\Common Files
C:\Users\WDAGUtilityAccount\Documents\Tencent Files -> Data\Documents\Tencent
C:\Users\WDAGUtilityAccount\Documents\WeChat Files -> Data\Documents\WeChat
C:\Users\WDAGUtilityAccount\Documents\WXWork -> Data\Documents\WXWork

其余的请查看 Tencent.wsb 中的 MappedFolder
```

