---
title: Arch安装使用指导
date: 2023-02-24 14:02:06
tags:
- linux
- arch
- pacman
---
# 前期准备
创建好虚拟机后打开虚拟机设置，在选项-高级中将firmware type更改为efi。
# 启动虚拟机，检查网络设置ssh
```zsh zsh
// 如果下面这个目录不存在则为bios模式启动
ls /sys/firmware/efi/efivars
// 检查网络是否通畅
ping baidu.com
// 使用下面指令更改密码并远程连接到
passwd
// 查看ip地址
ip addr
```
```cmd cmd
ssh -p 22 root@192.168.204.130
```
# 分区挂载
```zsh zsh
// 查看已存在的硬盘
lsblk
// 使用cfdisk创建分区
cfdisk /dev/nvme0n1
// 创建如下三个
Device           Start      End  Sectors  Size Type
/dev/nvme0n1p1    2048  1026047  1024000  500M EFI System
/dev/nvme0n1p2 1026048  9414655  8388608    4G Linux swap
/dev/nvme0n1p3 9414656 83884031 74469376 35.5G Linux filesystem
// 分别格式化
// 格式化EFI启动分区为fat32格式
mkfs.fat -F32 /dev/nvme0n1p1
// 格式化根目录为xfs文件系统
mkfs.xfs -f /dev/nvme0n1p3
// 格式化swap文件系统
mkswap /dev/nvme0n1p2
swapon /dev/nvme0n1p2
// 挂载
// 挂载根分区到mnt
mount /dev/nvme0n1p3 /mnt
// 新建EFI分区并挂载
mkdir -p /mnt/boot/EFI
mount /dev/nvme0n1p1 /mnt/boot/EFI
// 安装
// 使用下面命令下载并安装系统
pacstrap -i /mnt base base-devel vim linux linux-firmware
// 生成 fstab 文件并检查
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
// 切换系统目录
arch-chroot /mnt
// 更改时区，设置UTC
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc --utc
// 更改local并生成locale信息
vim /etc/locale.gen
// 找到合适的地区去掉前面的#
locale-gen
// 将语言信息写入locale.conf
echo LANG=en_GB.UTF-8 > /etc/locale.conf
// 配置网络
vim /etc/hostname
----
archlinux
// 直接写入自己的主机名
vim /etc/hosts
----
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain archlinux
// archlinux就是自己的主机名
// 安装相关包
pacman -S grub efibootmgr efivar networkmanager intel-ucode
// 配置grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
// 激活并启用NetworkManager
systemctl enable NetworkManager
// 更改密码
passwd
// 卸载挂载，重启
exit
umount /mnt/boot/EFI
umount /mnt
reboot 
```
# 安装后
```
// 配置网络，再次联网
// 输入nmtui 选择 “Activate a connection” 回车进入，选择你需要的网络，连接后back返回即可
// 安装 openssh
pacman -S openssh
systemctl start sshd
// 查看ip
ip -brief address / addr
// 修改ssh配置允许root连接
vim /etc/ssh/sshd_config
----
# 将下列的语句值改为yes
PermitRootLogin yes
// 重启ssh
systemctl restart sshd
// 使用ssh连接
ssh -o StrictHostKeyChecking=no root@192.168.204.130
```
