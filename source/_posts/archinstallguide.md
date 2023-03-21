---
title: Arch安装使用指导
date: 2023-02-24 14:02:06
tags:
- linux
- arch
- pacman
---
# 前期准备
在创建好虚拟机后，需要打开虚拟机设置，将 `firmware type` 更改为 `efi`，以便后续操作。
# 检查网络设置和 SSH 连接

以下是检查网络设置和 SSH 连接的操作步骤：

```zsh zsh
# 检查系统启动方式
ls /sys/firmware/efi/efivars

# 检查网络是否通畅
ping baidu.com

# 更改密码并远程连接
passwd

# 查看 IP 地址
ip addr
```
# 使用ssh连接
```cmd cmd
ssh -p 22 root@192.168.204.130
```

# 分区挂载
```zsh zsh
# 查看已存在的硬盘
lsblk
# 使用cfdisk创建分区
cfdisk /dev/nvme0n1
# 创建如下三个
Device           Start      End  Sectors  Size Type
/dev/nvme0n1p1    2048  1026047  1024000  500M EFI System
/dev/nvme0n1p2 1026048  9414655  8388608    4G Linux swap
/dev/nvme0n1p3 9414656 83884031 74469376 35.5G Linux filesystem
# 分别格式化
# 格式化EFI启动分区为fat32格式
mkfs.fat -F32 /dev/nvme0n1p1
# 格式化根目录为xfs文件系统
mkfs.xfs -f /dev/nvme0n1p3
# 格式化swap文件系统
mkswap /dev/nvme0n1p2
swapon /dev/nvme0n1p2
# 挂载
# 挂载根分区到mnt
mount /dev/nvme0n1p3 /mnt
# 新建EFI分区并挂载
mkdir -p /mnt/boot/EFI
mount /dev/nvme0n1p1 /mnt/boot/EFI
```


# 安装
```zsh zsh
# 使用下面命令下载并安装系统
pacstrap -i /mnt base base-devel vim linux linux-firmware
# 生成 fstab 文件并检查
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
# 切换系统目录
arch-chroot /mnt
# 更改时区，设置UTC
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc --utc
# 更改local并生成locale信息
vim /etc/locale.gen
# 找到合适的地区去掉前面的#
locale-gen
# 将语言信息写入locale.conf
echo LANG=en_GB.UTF-8 > /etc/locale.conf
# 配置网络
vim /etc/hostname
----
archlinux
# 直接写入自己的主机名
vim /etc/hosts
----
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain archlinux
# archlinux就是自己的主机名
# 安装相关包
pacman -S grub efibootmgr efivar networkmanager intel-ucode
# 配置grub
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
# 激活并启用NetworkManager
systemctl enable NetworkManager
# 更改密码
passwd
# 卸载挂载，重启
exit
umount /mnt/boot/EFI
umount /mnt
reboot 
```


# 安装后
```bash bash
# 配置网络，再次联网
# 输入nmtui 选择 “Activate a connection” 回车进入，选择你需要的网络，连接后back返回即可
# 安装 openssh
pacman -S openssh
systemctl enable sshd
systemctl start sshd
systemctl status sshd
# 查看ip
ip -brief address / addr
# 修改ssh配置允许root连接
vim /etc/ssh/sshd_config
----
# 将下列的语句值改为yes
PermitRootLogin yes
# 重启ssh
systemctl restart sshd
# 使用ssh连接
ssh -o StrictHostKeyChecking=no root@192.168.204.130
# 更新
pacman -Syu
useradd axro
vi /etc/passwd
---
```


# 优化终端
```zsh zsh
# 安装zsh，高亮，补全
sudo pacman -S zsh
sudo pacman -S zsh-autosuggestions
sudo pacman -S zsh-syntax-highlighting
# 安装git
sudo pacman -S git
# 使用以下命令配置zsh
autoload -Uz zsh-newuser-install
zsh-newuser-install -f
# 查看是否生成.zshrc
ls -a
# 安装oh my zsh
sh -c "$(curl -fsSL https:#raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# 在.zshrc中加入以下插件
vim .zshrc
---
ZSH_THEME="dieter"
alias vi=vim
alias ll='ls -l'
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
:wq
# 刷新终端
source .zshrc
# 安装neofetch
sudo pacman -S neofetch
---
11:18:08 axro@axro-arch ~ neofetch
                   -`                    axro@axro-arch
                  .o+`                   --------------
                 `ooo/                   OS: Arch Linux x86_64
                `+oooo:                  Host: VMware20,1 None
               `+oooooo:                 Kernel: 6.2.2-arch1-1
               -+oooooo+:                Uptime: 20 mins
             `/:-:++oooo+:               Packages: 182 (pacman)
            `/++++/+++++++:              Shell: zsh 5.9
           `/++++++++++++++:             Resolution: 1280x800
          `/+++ooooooooooooo/`           Terminal: /dev/pts/0
         ./ooosssso++osssssso+`          CPU: 11th Gen Intel i7-11800H (1) @ 2.304GHz
        .oossssso-````/ossssss+`         GPU: 00:0f.0 VMware SVGA II Adapter
       -osssssso.      :ssssssso.        Memory: 209MiB / 3883MiB
      :osssssss/        osssso+++.
     /ossssssss/        +ssssooo/-
   `/ossssso+/:-        -:/+osssso+-
  `+sso+:-`                 `.-/+oso:
 `++:.                           `-/+/
 .`                                 `/

11:21:14 axro@axro-arch ~
```


# 安装桌面环境
```zsh zsh
# 安装xorg-server，xorg-xinit
sudo pacman -S xorg-server xorg-xinit
# 安装显卡驱动
sudo pacman -S xf86-video-vesa # 这里使用的是vmware，请自行找适合自己机器的显卡驱动
# 安装plasma
sudo pacman -S pacman-meta konsole dolphin
sudo pacman -S kde-applications-meta sddm
# 启动kde plasma
sudo systemctl start sddm.service
# 安装字体，更改中文
sudo pacman -S noto-fonts-cjk wqy-microhei
# 安装火狐浏览器
sudo pacman -S firefox
# 使用如下命令打开和关闭网卡
ifconfig ens160 down/up
```


## vmware安装vmwaretools 实现窗口分辨率自适应。
```zsh zsh
# 安装
sudo pacman -Sy gtkmm gtkmm3 net-tools open-vm-tools xf86-video-vmware xf86-input-vmmouse
sudo systemctl enable vmtoolsd.service
sudo systemctl enable vmware-vmblock-fuse.service
sudo reboot
```
### 详细请参考 
[wiki.archlinuxcn.org](https://wiki.archlinuxcn.org/wiki/VMware/%E5%AE%89%E8%A3%85_Arch_Linux_%E4%B8%BA%E8%99%9A%E6%8B%9F%E6%9C%BA)
这里不多赘述。
## 安装中文输入法
```zsh zsh
// 安装fcitx5
sudo pacman -S fcitx5-im fcitx5-chinese-addons  fcitx5-rime fcitx5-configtool
// 编辑配置文件
vi /etc/environment
---
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx

// 重启，gnome和kde会自动配置
reboot
```

## 安装yay工具并安装linuxqq
```zsh zsh
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si

```

## 编写rc.local
```
vi /usr/lib/systemd/system/rc-local.service
---
#/usr/lib/systemd/system/rc-local.service
[Unit]
Description="写下描述" 

[Service]
Type=forking
ExecStart=/etc/rc.local start #在这里你可以看到开机执行脚本的位置
TimeoutSec=0
StandardInput=tty
RemainAfterExit=yes
SysVStartPriority=99

[Install]
WantedBy=multi-user.target

vi /etc/rc.local
---
#/bin/bash
sudo pacman -Syu
exit 0

chmod 777 /etc/rc.local
systemctl enable rc.local.service
```