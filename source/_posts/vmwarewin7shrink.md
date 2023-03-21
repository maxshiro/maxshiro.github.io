---
title: vmwarewin7shrink
date: 2023-01-14 17:41:37
tags:
- vmware
- win7
- capacity
- shrink
---
通常在我们创建虚拟机时往往并不清楚需要多少空间，等到后期才会发现分配了很多空间实际上用不到。这时候就要想办法缩小虚拟机所占用的空间了。
根据我这次缩小空间的经历，我十分不推荐使用快照功能。因为这会让你虚拟机当前处在快照模式，与原来的文件关系不大（就是处理起来更麻烦了。）。
如果你像我一样只是用vmware来存放如企鹅，钉钉，腾讯会议，微信这种垃圾的话，那么可以参照我的方法。
# 缩小空间占用。
## 0.首先查看磁盘类型
使用文本编辑器打开虚拟机根目录下的 xxx.vmdk 文件，查看 # Extent description 下面的内容第三块是否为 SPARSE 。不是则需要进行转换。如下图：
```
RW 8323072 SPARSE "Windows 7 x64-cl1-s001.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s002.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s003.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s004.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s005.vmdk"
RW 4521984 SPARSE "Windows 7 x64-cl1-s006.vmdk"
```
如果你在创建虚拟机选择的是动态分配磁盘文件并拆分成多个的话这里就是 SPARSE。
## 1.确保没有快照
确保虚拟机内没有快照。虚拟机目录下只有一个xxx.vmdk以及从属的s00x.vmdk。如果有快照没法处理，则可以执行克隆操作到其他盘进行处理。
## 2.尽可能缩小虚拟机内部所占用的空间
> 参考[【Win10 C盘压缩卷问题解答】：无法将卷压缩到超出任何不可移动的文件所在点](https://blog.csdn.net/CoutCodes/article/details/104975783)

进入虚拟机，进行以下处理：
* 关闭休眠功能*
* 关闭虚拟内存并删除虚拟内存文件*
* 虚拟机内进行空间整理，磁盘清理
* 处理其他文件

注意后面带星号的处理完成所有步骤后要重新打开(建议)，不然可能会影响性能。

此时你需要确定你缩小后的c盘空间大小，这也决定着后面动态磁盘缩小的操作。我确定缩小后的空间为22GiB。
## 3. 重启到pe系统使用Diskgenius缩小c盘空间。
### 使用微pe制作iso文件
1. 打开
### 添加硬件CD/DVD设备，使用创建的iso文件。

### [VMWare设置光驱启动](https://blog.csdn.net/syf442/article/details/5067832)
## 4. 减小VMware虚拟机虚拟磁盘大小
> 此处内容来自：[减小VMware虚拟机虚拟磁盘大小](https://blog.csdn.net/HayPinF/article/details/108252631)

在 VMware 里虚拟机的所有编号了的 `.*s001.*.vmdk` 片段虚拟磁盘文件是虚拟化的虚拟机最大空间（Maximum Size)，就比如我的 Win7 虚拟机的Maximum Size是40GiB（1GiB=1024MiB，1GB=1000MB），虚拟机生成了 `.*s001~s011.vmdk` 共11个虚拟机磁盘文件 vmdk，该虚拟机的"Windows 7 x64-cl1.vmdk"中详实记录了这11个vmdk文件（从而进行各分片虚拟磁盘文件的按名索引）：
```
# Extent description
RW 8323072 SPARSE "Windows 7 x64-cl1-s001.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s002.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s003.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s004.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s005.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s006.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s007.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s008.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s009.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s010.vmdk"
RW 655360 SPARSE "Windows 7 x64-cl1-s011.vmdk"
```
vmdk文件的"8323072"数字表示簇，2簇=1KiB，则这11个vmdk文件对应了((10×8323072)/2/1024/1024)+(655360/2/1024/1024)=10×3.96875GiB+0.3125Gib=40GiB，刚好。

我们所要做的就是通过改变这里的文件个数从而达到缩小vmdk磁盘大小的目的。
已知40GiB对应的簇大小为10×8323072+655360=83886080。即一个G的簇大小为2097152(/2=1048576)。
我们确定一下自己压缩后的大小。就比如我要压缩到22G，得出簇的总大小为2097152×22=46137344，这个数可以拆为五个8323072加上一个46137344-(8323072×5)=4521984。即(8323072×5)+4521984=46137344。
因此上面的文件就可以改成下面这样：
```
# Extent description
RW 8323072 SPARSE "Windows 7 x64-cl1-s001.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s002.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s003.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s004.vmdk"
RW 8323072 SPARSE "Windows 7 x64-cl1-s005.vmdk"
RW 4521984 SPARSE "Windows 7 x64-cl1-s006.vmdk"
```
记得备份"Windows 7 x64-cl1.vmdk"，处理完成保存即可。
## 5.重新启动虚拟机
此时你会发现虚拟机的磁盘大小已经变成22G了。教程结束。