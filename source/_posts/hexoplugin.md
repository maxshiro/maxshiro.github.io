---
title: 使用插件巧妙完善你的hexo博客
date: 2022-08-28 22:25:43
cover: http://axro.top:85/i/2022/08/23/6304d375b585c.png
tags: 
- plugin
- hexo
---
# Intro
上一回我们讲到了如何部署hexo到github上，并简单的向repo里推送了你的博客。这一回我们简单的使用几个插件来完善你的博客。
# 1. 博客加密
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