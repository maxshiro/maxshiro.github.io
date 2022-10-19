---
title: linuxlearn2
date: 2022-09-27 08:28:33
tags:
---
---
title: linuxlearn2
date: 2022-10-18 11:16:47
tags:
- diff
- sudoers
- passwd
- usermod
- linux
---
* diff命令
diff 参数 文件1 文件2 > 新文件

netstat -ano | grep "80" 

* sudoers
%开头表示用户组
权限:
/sbin:
该目录下的命令通常只有管理员才可以运行
/bin：
下的命令管理员和一般的用户都可以使用。
/usr/sbin：
这个目录表示系统命令的位置
/usr/bin:
存放一些用户命令，如led(控制LED灯的)。

* passwd
创建用户权限
/etc/passwd
用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
useradd -u uid

* usermod & groupmod
// 修改用户所属组
usermod -g 用户组 用户名
// 修改用户ID
usermod -u UID 用户名
// 修改组ID
usermod -g GID 组名