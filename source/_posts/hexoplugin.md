---
title: 部署完hexo后还应该准备哪些工作
date: 2022-08-28 22:25:43
sticky: true
cover: img/hexoplugin/20220920213944.png
tags: 
- plugin
- hexo
- backup
---
# Intro
上一回我们讲到了如何部署hexo到github上，并简单的向repo里推送了你的博客。这一回我们简单的来完善你的博客。
# 0.shoka主题配置
> [Click Me](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/display/ 'Click Me')

# 1.备份Hexo源文件到 Backup 分支
由于种种原因我们总会更改自己的工作环境或者一顿操作猛如虎，将自己博客的源文件一通收拾，然后就没有然后了。
所以建议大家创建好博客记得把源文件也一同备份到 Github 上以防万一。
## 创建backup分支
1. 打开你GitHub repo的页面，点击左上角的分支选项。
2. 点击 View all branches，然后点击右上角的 New branch。
3. Branch name：backup，Branch source：master
4. create branch。
## 将backup分支切换为默认分支
> 我也不知道为什么要这么做，但是不这么做最后一步就没法push了 :(
## 将repo clone到本地
1. 直接在博客根目录下执行这个命令：
```
git clone '你的repo'
```
2. 将克隆过来repo里的`.git`文件夹拷贝到博客根目录。
```
cp -r ./'你的repo' ./
```
3. 依次执行以下指令
```
// 保存所有文件到暂存区
git add .
// 提交变更
git commit -m 'Backup'
// 推送到github，分支为Backup
git push origin backup
```
## 报错
可能会提示如下错误
```
fatal: unable to access 'https://github.com/xxx/xxx.github.io.git/': GnuTLS recv error (-110): The TLS connection was non-properly terminated.
```
执行下面的指令即可正常推送
```
git config --global --unset http.proxy
git config --global --unset git.proxy
```
## 还原
1. 克隆backup分支到本地
```
git clone -b backup https://github.com/maxshiro/maxshiro.github.io.git
```
2. 安装依赖
```
cnpm install
```

# 2. 博客加密
* 有时候我们可能需要写一些私密的博客，通过密码验证的方式让人不能随意浏览。
* 这在wordpress，emlog或其他博客系统中都很容易实现，然而hexo除外。:(
* 为了解决这个问题，我们需要安装“ hexo-blog-encrypt”扩展。
## 安装指令
```
npm install hexo-blog-encrypt
```
## 快速使用
将 password 标签添加到您的 Front-matter 里，就像这样：
```
---
title: xxx
date: 2022-08-26 17:34:59
password: 233
tags: xxx
---
```
## 特性
* 一旦你输入了正确的密码，它就会被存储在本地浏览器的localStorage中。按个按钮，密码将会被清空。若博客中有脚本，它将被正确地执行。
* 支持按标签加密。
* 所有的核心功能都是由原生的API所提供的。在Node.js中，我们使用Crypto。在浏览器中，我们使用Web Crypto API。
* PBKDF2，SHA256被用作复制密钥，AES256-CBC被用作加解密，我们还使用HMAC来验证密文的来源，并确保其纠正。
* 广泛地使用Promise来进行异步操作，从而确保线程不被杜塞。
* 过时的浏览器将无法正常显示，因此，请升级您的浏览器。

