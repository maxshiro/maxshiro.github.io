---
layout: wiki
wiki: dvwa
breadcrumb: false
title: 反射型XSS
order: 204
comments: false
---

## 什么是命令注入：

命令注入就是在需要输入数据的地方输入了恶意代码，而且系统并没有对其进行过滤或者其他处理导致恶意代码也被执行，最终导致数据泄露或者正常数据被破坏。

常用到的命令：（总结来说就是系统操作命令DOS命令都可以在此执行试试）

ipconfig，net user（查看系统用户），dir（查看当前目录），find（查找包含指定字符的行），whoami（查看系统当前有效用户名）A&B（简单的拼接，AB之间无制约关系），A&&B（A执行成功才会执行B），A|B（A的输出作为B的输入），A||B（A执行失败，然后才会执行B）