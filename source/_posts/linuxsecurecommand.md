---
title: 常用linux应急响应命令整理
categories: [应急响应]
date: 2023-07-02 21:52:54
tags:
- linux
- security
- 应急响应
- hvv
- tools
- busybox
- ps
cover: /img/linuxsecurecommand/2023-07-02-22-04-25.png

---

## 常用命令
### ps -auxf

```
ps -auxf
```

此命令用于显示系统中当前运行的进程。`-a`选项显示所有用户的进程，`-u`选项显示进程的详细信息，`-x`选项显示没有控制终端的进程，`-f`选项以全格式显示进程信息。

### netstat -antlp | more

```
netstat -antlp | more
```

此命令用于查看系统中的网络连接和监听端口。`-a`选项显示所有连接和监听端口，`-n`选项以数字形式显示地址和端口，`-t`选项只显示TCP连接，`-l`选项只显示监听端口，`-p`选项显示与进程关联的端口，`| more`用于分页显示输出结果。

### ls -alt /proc/pid号

```
ls -alt /proc/pid号
```

此命令用于查看指定进程的详细信息。`ls`命令用于列出文件和目录，`-a`选项显示所有文件和目录（包括隐藏文件），`-l`选项以长格式显示详细信息，`-t`选项按修改时间排序，`/proc`是一个特殊目录，包含系统中运行的进程的信息，`pid号`是要查看的进程的进程ID。

### ls -alh /proc/pid

```
ls -alh /proc/pid
```

此命令用于查看指定进程的详细信息。`-a`选项显示所有文件和目录（包括隐藏文件），`-l`选项以长格式显示详细信息，`-h`选项以人类可读的方式显示文件大小，`/proc`是一个特殊目录，包含系统中运行的进程的信息，`pid`是要查看的进程的进程ID。

### lsof -i:端口号 /-p /-u

```
lsof -i:端口号 /-p /-u
```

此命令用于查看指定端口的相关进程或指定用户打开的文件。`lsof`命令用于列出打开的文件，`-i:端口号`选项显示指定端口相关的进程，`/-p`选项显示指定进程打开的文件，`/-u`选项显示指定用户打开的文件。

### 挖矿病毒常利用手段

```
chattr +i
lsattr
```

- `chattr +i`：此命令用于改变文件的属性。`chattr`命令用于改变文件的扩展属性，`+i`选项设置文件为不可修改属性，防止文件被删除、重命名或修改。

- `lsattr`：此命令用于显示文件的扩展属性。`lsattr`命令用于

列出文件的扩展属性，包括文件的可修改性、不可修改性、是否加密等。

### 需要注意的几个目录
```
/etc/passwd
/etc/rc.local
/root/.ssh
/tmp
/etc/init.d
/etc/hosts
```

此路径对应着系统启动时执行的脚本文件。`/etc/rc.local`文件包含在系统启动过程中自动执行的命令，可以用于配置系统启动时需要运行的自定义脚本。

### ls -alt /etc/profile.d/*.sh

```
ls -alt /etc/profile.d/*.sh
```

此命令用于列出`/etc/profile.d/`目录下以`.sh`为后缀的脚本文件，并按照修改时间排序显示详细信息。`ls`命令用于列出文件和目录，`-a`选项显示所有文件和目录（包括隐藏文件），`-l`选项以长格式显示详细信息，`-t`选项按修改时间排序，`/etc/profile.d/*.sh`表示匹配`/etc/profile.d/`目录下以`.sh`为后缀的所有文件。

### cat /etc/passwd | cut -f 1 -d : | xargs -l {} crontab -l -u {}

```
cat /etc/passwd | cut -f 1 -d : | xargs -l {} crontab -l -u {}
```

此命令用于查看系统中所有用户的定时任务。`cat`命令用于显示文件内容，`/etc/passwd`是一个包含系统用户信息的文件，`cut`命令用于提取指定字段，`-f 1`选项指定提取第一个字段（用户名），`-d :`选项指定字段的分隔符为冒号，`xargs`命令用于将前面的输出作为参数传递给后面的命令，`-l`选项表示逐行处理，`{}`表示参数的占位符，`crontab -l -u {}`用于查看指定用户的定时任务列表。



## 软件包校验

- `rpm -Va`：校验 RPM 软件包的完整性和一致性。如果输出中出现以下标记，表示不一致：s (size)、m (modify)、5 (MD5)、d (device)、l (link)、u (user)、g (group)、t (time)。特别需要注意的是 SM5 标记。

- `dpkg -verify`：校验 Debian 软件包的完整性和一致性。

## 文件操作

- `stat`：显示文件或文件系统的详细信息。

- `find / -mtime 0 -name *.*`：查找指定天数内更改过的文件。

- `find / -ctime 0 -name *.*`：查找新增的文件。

- `diff -c`：比较文件的差异并生成上下文格式的差异。

## 查找隐藏进程

- ```
  ps -ef | awk '{print $2}' | sort -n | uniq > ps.p
  ls /proc | sort -n | uniq > proc.p
  ```

  查找隐藏进程的命令。

## SSH 爆破

- `last/lastb/lastlog`：查看登录记录。

## 日志

- `/var/log/secure`：安全日志文件。

  - `awk '/sshd.*Failed/{print $(NF -3)}' /var/log/secure | sort | uniq -c | sort -nr`：查找每个 IP 登录失败的次数。
  - 每条记录的格式：时间-主机名-服务名称-是否成功-用户-地址
  - `sudo grep 'sshd.*Failed.*from <IP_ADDRESS>' /var/log/secure | awk '{print $1,$2,$3,$9,$11}'`：查找特定 IP 失败的次数。

  - `sudo grep 'sshd.*Accepted.*from <IP_ADDRESS>' /var/log/secure | awk '{print $1,$2,$3,$9,$11}'`：查找特定 IP 成功的次数。

- `/var/log/cron`：定时任务日志文件。

- `/var/log/btmp`：登录失败日志文件。

- `/var/log/wtmp`：登录记录日志文件。

## 工具集

- `busybox`：busybox 是一个包含多个常用 Linux 命令精简版本的工具集。它可以在嵌入式系统或资源受限的环境中使用，将多个命令集成到一个可执行文件中，以节省空间并提高效率。它可以作为替代系统中关键命令的解决方案，因为它是一个独立的可执行文件，不依赖于系统中的命令。

  - `./busybox netstat -antpl`：使用 busybox 的 netstat 命令显示网络连接和监听端口的信息。

## 隐藏进程检测工具

- `unhide`：用于探测隐藏进程的工具。

## Rootkit 检测工具

- `chkrootkit -n`：用于探测 rootkit 的工具。`-n`选项表示以非交互模式运行。

- `rkhunter`：另一个用于探测 rootkit 的工具。

## 恶意软件扫描工具

- `clamav`：用于扫描和检测恶意软件的工具。

## Webshell 查杀工具

- `www.shellpub.com`：河马webshell查杀工具。

## 内存马查杀工具

- `java-memshell-scanner`：用于查杀内存马（内存中的恶意代码）的工具。