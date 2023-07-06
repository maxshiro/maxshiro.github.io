---
title: 使用 Hexo 程序部署博客到githubpage上
date: 2022-04-17 13:35:37
cover: /img/hexoplugin/20220920213944.png
tags: 
- hexo
- guide
---
# 环境
Hexo程序使用node.js程序编写，因此需要现在环境机上部署node.js，git只用来推送。
> **什么是 Node.js**：一个运行javascript(JS)的环境。Hexo 是一个javascript(JS)语言的程序。一般 JS 运行在浏览器中，但是也有一种非浏览器环境也可以运行 JS。这个环境是Node.js

> **什么是 Git**：一个版本控制软件。Hexo 博客源码由纯文本构成，管理纯文本项目的最好办法之一就是使用Git。还记得 word 里面的历史记录和版本回退功能么？简单的来说，Git 就提供了类似功能，只不过更强大。

> 本文章使用fedora系linux操作。
## 安装 git
使用yum指令进行安装：
```bash
su
yum install git-core # 坏习惯，请减少使用root用户
```
查看是否安装成功：
```bash
[root@localhost ~]# git --version
git version 1.8.3.1
```
## 安装 node.js
本来可以直接使用`apt，yum`这种工具一键安装的，但是显然安装的版本十分的stable，所以这里使用 nvm 工具来安装 nodejs 和 npm。
```bash
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ./.bashrc
nvm -v
# 安装最新版node并更新一下npm
nvm install node --latest-npm
```
然后查看版本：
```bash
[root@localhost ~]# node -v
v16.14.2
```
顺便看看npm版本：
```bash
[root@localhost ~]# npm -v
8.5.0
```
# 配置本体
## hexo
如果你没有使用加速等工具的话，这里建议将npm源改到淘宝：
```bash
npm config set registry http://registry.npm.taobao.org
```
使用npm指令安装：
```bash
npm install -g hexo-cli
```
看看安装好了没：
```bash
[root@localhost ~]# hexo -v
hexo-cli: 4.3.0
os: linux 3.10.0-1160.31.1.el7.x86_64 CentOS Linux 7 (Core)
node: 16.14.2
...
ngtcp2: 0.1.0-DEV
nghttp3: 0.1.0-DEV
```
## 安装server
如果你想在本地预览博客的话，可以安装下。其实装不装都无所谓。
```bash
npm install hexo hexo-server
```
## 安装推送插件
这个推送插件是后面要推送到`github page`上要使用的插件。
```bash
npm install hexo-deployer-git --save
```
## 创建博客
```bash
hexo init [你的博客名字]
cd [你的博客名字]
npm install
```

> 如果你已经备份过想要还原，则只需要做到这一步就已经可以了。但是如果想要推送还需要再看下面的配置git项。

# 本地预览博客

上面已经生成好了基本博客，可以使用`ls`命令查看下是否正确生成了文件
下面是hexo博客的基本结构。
```bash
.
 ├── _config.yml # 网站配置信息
 ├── package.json # 应用程序信息
 ├── scaffolds # 模板文件夹
 ├── node_modules # 生成博客后编译出来的文件。主要看这个是否生成成功了。
 ├── source # 存放用户资源
 |   ├── _drafts
 |   └── _posts
 └── themes # 主题文件夹
```
如果确认没什么问题便可以进行生成步骤了。

1. 测试本地生成博客
```bash
# 生成一篇文章，注意要在blog的目录下。
hexo new "文章名"
# 生成静态网页
hexo g
# 打开本地服务器
hexo s
```
此时你可以打开 `[博客地址]:4000` 来查看效果了。
如果确认没问题，便可以继续操作。

# 备份还原
博客部署完成后或是以后工作中难免需要更换环境，这里也简单说一下备份还原的流程。
## 本地备份还原
本地备份还原是对主要文件自行压缩拷贝后导出，然后覆盖进行还原
> 需要备份的文件列表如下
```bash
.
|-- .gitignore
|-- _config.yml
|-- package-lock.json
|-- package.json
|-- scaffolds
|-- source
`-- themes
```
将这些文件考出来后则备份完成。

## 可能会出现的问题

如果当你完成还原，进行`hexo g`命令提示如下错误：
```bash
root@LAPTOP-N4RDFRJ0:/mnt/d/Program/hexo/blog# npm install --save bulma-stylus
npm ERR! code FETCH_ERROR
npm ERR! errno FETCH_ERROR
npm ERR! invalid json response body at https://registry.npmjs.org/browserslist reason: Unexpected end of JSON input

npm ERR! A complete log of this run can be found in:
npm ERR!     /root/.npm/_logs/2022-08-23T09_22_43_977Z-debug.log
```
执行这条命令即可：
```bash
npm cache clean --force
```

# 配置远端
我将博客部署到了github上，所以你要先去github.com上注册一个账户。并创建一个仓库。
## 初始化 git
```bash
init git
```
> 如果提示`Couldn't find an alternative telinit implementation to spawn.`不用管，直接下一步。
## 配置 ssh

1. 配置登录信息
```bash
git config --global user.name "你的git用户名"
git config --global user.email "你的git登录邮箱"
```
2. 生成ssh keygen
```bash
ssh-keygen -t rsa -C "你的git登录邮箱"
```
> 如果弹出了很多内容，直接进行一个回车跳过。
```bash
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
... // 直接回车跳过
```
3. 复制密钥
```bash
vi /root/.ssh/id_rsa.pub
```
> 将里面显示的所有内容复制。
4. 登录github
* 头像
* Settings
* SSH and GPG keys
* New SSH key
* 将复制的内容粘贴到Keys框里
* Add SSH key
5. 测试链接是否成功
```bash
[root@localhost ~]# ssh -T git@github.com
Hi maxshiro! You've successfully authenticated, but GitHub does not provide shell access.
```

# 配置推送
配置完git之后便可以配置hexo博客的推送地址了，下面以我的为例。

1. 首先cd到当前目录
```bash
cd blog
```
2. 编辑配置文件
```bash
vi _config.yml
```
3. 移动到 deploy 天机如下内容
```bash
deploy:
    type: git
    reop: git@github.com:maxshiro/maxshiro.github.io.git #这个地址可以到你的repo那里获得。
    branch: main #这个branch就是仓库的默认目录了。
```

# 推送博客
已经测试并生成过博客了，所以可以直接推送。
```bash
# 清理之前生成的文件
hexo clean
# 生成静态网页
hexo g
# 推送到github repo
hexo d
```

# 结语
此时你已经完成了hexo博客的基本配置。
