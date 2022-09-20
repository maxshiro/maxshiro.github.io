---
title: 解决linux下挂载其他linux硬盘的问题并创建smb局域网共享
date: 2022-08-28 21:15:02
cover: /img/linuxsmb/20220920214113.png
tags:
- linux
- 运维
- smb
---
# Intro
> 之前用来当作小型服务器的笔记本终于被我给淘汰了.已经关机了好久.最近买了块`SATA`硬盘盒,想要将这台笔记本内的数据备份出来并把硬盘用作其他用途.一切连接好后发现无法挂载,遂百度了一会查出了如下方法...

# 故障复现
1. 连接linux系统,提示插入硬盘.`/dev`分区下出现硬盘编号
2. 执行如下指令
```
cd /
mkdir backup
mount /dev/sdc/ /backup/
```
报错
```
mount: unknown filesystem type 'LVM2_member'
```

# 解决方法 //Debian系

1. 执行如下指令安装lvm2并扫描查看以挂载的磁盘
```
sudo apt-get install lvm2
sudo vgscan
```
2. 发现如下字样后找到对应的`xxx`
```
Found volume group "xxx" using metadata type lvm2
```

3. 设为活动状态并查看可挂载的分区

```
sudo vgchange -ay xxx
sudo lvs
LV     VG   Attr   LSize  Origin Snap%  Move Log Copy%  Convert
data        xxx             -wi-a-              34.19G      
```

4. 挂载对应分区
```
sudo mount /dev/xxx/data/ /backup/
```

> 至此已经解决了问题,如果你还想把这块硬盘里的数据共享出去,比如共享给局域网内的windows主机,那就接着看..

# 设置SMB

1. 先更新一下

```
sudo apt-get update && sudo apt-get upgrade
```

2. 安装Samba服务
```
sudo apt-get install samba samba-common
```

3. 配置共享目录
```
// 新建目录
sudo mkdir /home/db
// 更改目录权限
sudo chmod 777 /home/db/ -R
```

4. 添加账户
> 要注意添加的账户需要是系统内已存在的账户
```
// 后面的pi为系统的用户名
sudo smbpasswd -a pi
```
> 输入两次密码后创建成功

* 创建系统账户
```
// -m参数为添加/home/username目录,username更改为你要创建的用户名.
sudo useradd -m username
// 更改账户密码
sudo passwd username
// 删除用户
sudo userdel -r username
// 更改登录zhanghu
su -username
```

5. 配置samba
```
// 建议先备份一下
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
// 编辑
sudo vim /etc/samba/smb.conf
```
> 在文件最后添加

```
[database] 
    comment = 23333333  # 描述
	path = /home/db/    # 目录
	public = yes        # 是否允许guest用户访问
	writable = yes      # 是否允许读写
	workgroup = WORKGROUP  # 工作组
```

6. 重启samba服务
```
sudo service smbd restart
```

然后在windows里面映射就行了

# 借鉴(摘抄)

[ubuntu 20.04 安装配置Samba服务，Windows 和 linux协同工作](https://blog.csdn.net/mvp_Dawn/article/details/105847485 "ubuntu 20.04 安装配置Samba服务，Windows 和 linux协同工作")
