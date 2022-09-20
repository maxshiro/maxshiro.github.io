---
title: 解决Windows直接访问WSL2路径并直接进行读写操作,权限不足的问题
date: 2022-08-28 23:42:36
cover: img/wsl2permission/20220920220343.png
tags:
- wsl
- 权限不足
- vscode
---
# Intro
初次安装WSL Ubuntu时，一般都会设置默认登录用户，当你通过windows访问该文件夹时，如果访问的Ubuntu目录的权限用户时你的默认用户，你可以对其进行修改。但是如果你访问的时root权限的用户，则会出现如权限不足，permission deny报错。
# Solution
```
C:\Users\wangj\AppData\Local\Microsoft\WindowsApps\debian.exe config --default-user root
```
修改完成后，记得重启wsl：
```
wsl --shutdown
```

10s后，在执行wsl命令，然后再次测试，看是否可行。
