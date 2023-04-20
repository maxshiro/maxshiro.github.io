---
title: 通过diskpart工具压缩userdata.vhdx来解决磁盘占用过多问题
date: 2022-11-18 20:43:50
img: img/Windowssubsystemforandroidcompression/20221118204548.png
tags:
- wsa
- vhd
- vhdx
- diskpart
---
# Intro
当你安装了WSA后随着你的使用，你会发现占用的空间越来越大。即使你把不需要的安卓程序卸载了之后，却依然满满的霸占着你的C盘。你只能眼睁睁地看他慢慢变大却无能为力。当然本篇文章的目的就是帮你解决这个问题。
# 移动WSA到其他盘符
> 你利用你聪明的头脑打开了百度并使用灵巧的双手在搜索框里输入WSA磁盘空间过大。很快你便看到了一种解决方式.
1. 打开设置 - 程序 - 已安装的程序，找到WSA
![](/img/Windowssubsystemforandroidcompression/20221118205416.png) 
2. 点击后面三个点选择移动。
稍等一会就会帮你把WSA移动到对应的盘符了。
# 使用diskpart工具压缩
1. 以管理员身份运行cmd，输入diskpart命令按回车。
2. 使用以下命令选择你的userdata.vhdx
```cmd cmd
select vdisk file = "D:\WpSystem\S-1-5-21-3846526574-1502088438-2560978845-1001\AppData\Local\Packages\MicrosoftCorporationII.WindowsSubsystemForAndroid_8wekyb3d8bbwe\LocalCache\userdata.vhdx"
```
如果提示
```cmd cmd
DiskPart successfully selected the virtual disk file.
```
就说明成功了。

3. 输入 `compact vdisk` 来压缩。
```
DiskPart has encountered an error: The specified file is encrypted and the user does not have the ability to decrypt it.
See the System Event Log for more information.
```
这个就说明你没有权限压缩这个文件。
为了解决这个问题我百度了一晚上，最后发现，只要你把这个文件复制走，就会提示
![](/img/Windowssubsystemforandroidcompression/20221118210314.png)  
这里选择是，然后就会复制走。
这个时候你再看你复制的文件就会发现上面的小黄锁已经不见了。

4. 重新选择userdata.vhdx文件
```cmd cmd
select vdisk file = "D:\userdata.vhdx"
```
5. 重新输入命令 `compact vdisk` 进行压缩,如果出现下面的提示那就说明压缩成功了。
```cmd cmd
DISKPART> compact vdisk

  100 percent completed

DiskPart successfully compacted the virtual disk file.

DISKPART>
```
此时再看你复制出来的文件，就会发现大小缩小了很多，压缩成功了。
接着只需要剪切拷贝出来的文件，替换掉原来的文件就可以了。

# 成果
**压缩前：**
> ![](/img/Windowssubsystemforandroidcompression/20221118204548.png)  

**压缩后：**
> ![](/img/Windowssubsystemforandroidcompression/20221118210939.png)  