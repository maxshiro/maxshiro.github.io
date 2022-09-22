---
title: Linux入门学习0
date: 2022-09-22 17:52:33
hidden: true
tags:
- linux
- 目录
- 指令
- 权限
---

# linux系统目录结构
> 在linux中，所有都是以文件的形式存储和管理的。
## /bin
> Binary 存放经常使用的命令。
## /sbin
> super Binary 超级用户命令存放的目录。
## /home
> 用户目录
## /root
> 系统管理员目录
## /etc
> 管理系统配置文件
## /usr
> 类似于windows下Programfiles目录，
## /boot
> 启动所使用的核心文件都会存放在这里。
## /proc
> 内存映射
## /srv
> services的缩写，用于存放服务启动所需要的数据
## /sys
> 系统文件
## /tmp 
> 是temporary(临时) 的缩写，存放临时/缓存文件，可当作回收站使用。
## /dev
> device的缩写，类似于windows的设备管理器。
## /mnt
> linux用于挂载可移动设备（临时）的目录。
## /opt
> 主机额外安装的软件存放的目录
## /var
> 存放经常被修改的目录或文件，比如日志（*.log）。
## /lib
> 存放系统基本的动态链接库。

# 权限数字意思详解
我们首先打开控制台在任意位置输入ll,就能看到当前文件夹下所有类型的信息.我们只取其中的前十位来进行结束.
> dr-xr-xr-x.
## 第一位
> 第一位表示类型
* d directory 表示目录
* b block 块文件
* c char 字符文件
* \- file 普通文件
* l link 链接
* etc.

## 后面九位
> 后面九位表示了文件的类型,对应的位置如果是`-`则表示没有对应的权限.

<table>
    <th>
        <td colspan="3">前三位</td>
        <td colspan="3">中间三位</td>
        <td colspan="3">后三位</td>
    </th>
    <tr align="center">
        <td colspan="3">创建者</td>
        <td colspan="3">同组用户</td>
        <td colspan="3">其他用户</td>
    </tr>
    <tr align="center">
        <td>r</td>
        <td>w</td>
        <td>x</td>
        <td>r</td>
        <td>w</td>
        <td>x</td>
        <td>r</td>
        <td>w</td>
        <td>x</td>
    </tr>
    <tr align="center">
        <td>读</td>
        <td>写</td>
        <td>执行</td>
        <td>读</td>
        <td>写</td>
        <td>执行</td>
        <td>读</td>
        <td>写</td>
        <td>执行</td>
    </tr>
</table>

下面来看一个实例:

如果一个文件的权限为755,则代表什么意思?

我们只需要把755按位转换为二进制,对应位为0则表示没有这个权限.

|7|5|5|
|-|-|-|
|111|101|101|

用人的语言表述:
> 对于创建者:可以读取,可以更改,可以执行.
> 对于同组人员:可以读取,不能更改,可以执行.
> 对于其他成员:可以读取,不能更改,可以执行.
