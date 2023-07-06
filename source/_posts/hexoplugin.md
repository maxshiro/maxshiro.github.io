---
title: 部署完hexo后还应该准备哪些工作
date: 2022-08-28 22:25:43
cover: /img/hexoplugin/20220920213944.png
top: 2
categories: [Hexo]
tags: 
- plugin
- hexo
- backup
---
# Intro
上一回我们讲到了如何部署hexo到github上，并简单的向repo里推送了你的博客。这一回我们简单的来完善你的博客。
# 0.主题配置
* old:
[shoka](https://shoka.lostyu.me/computer-science/note/theme-shoka-doc/display/ 'Click Me')
[matery](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md 'matery')
* new
[stellar](https://xaoxuu.com/wiki/stellar/#start 'stellar')
[volantis](https://volantis.js.org/v6/getting-started/ 'volantis')

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
```bash
git clone '你的repo'
```
2. 将克隆过来repo里的`.git`文件夹拷贝到博客根目录。
```bash
cp -r ./'你的repo' ./
```
3. 依次执行以下指令
```bash
// 保存所有文件到暂存区
git add .
// 提交变更
git commit -m 'Backup'
// 推送到github，分支为Backup
git push origin backup
```
## 报错
可能会提示如下错误
```bash
fatal: unable to access 'https://github.com/xxx/xxx.github.io.git/': GnuTLS recv error (-110): The TLS connection was non-properly terminated.
```
执行下面的指令即可正常推送
```bash
git config --global --unset http.proxy
git config --global --unset git.proxy
```
## 还原
1. 克隆backup分支到本地
```bash
git clone -b backup https://github.com/maxshiro/maxshiro.github.io.git
```
2. 安装依赖
```bash
cnpm install
```

# 安装一些提升体验的插件
hexo原版的内容已经足够使用，但是某些功能非常的需要但是hexo却不自带，那么就可以通过安装插件的形式来添加这些功能。
## 置顶文章
```bash
# 先卸载原本的index生成插件
npm uninstall hexo-generator-index
# 然后安装修改过后的
npm install hexo-generator-index-pin-top --save
```
使用时只需要在front-matter中加入`top: true`即可让文章置顶。

## 隐藏文章
```bash
# 使用如下命令安装插件
npm install hexo-hide-posts --save
```
安装好后在`_config.yaml`文件中添加下面的内容：
```_config.yaml bash
# hexo-hide-posts
hide_posts:
  # 可以改成其他你喜欢的名字
  filter: hide
  # 指定你想要传递隐藏文章的位置，比如让所有隐藏文章在存档页面可见
  # 常见的位置有：index, tag, category, archive, sitemap, feed, etc.
  # 留空则默认全部隐藏
  public_generators: []
  # 为隐藏的文章添加 noindex meta 标签，阻止搜索引擎收录
  noindex: true
```
最后同上，你只需要在`front-matter`中加上`hide: true`即可。

## 博客加密
* 有时候我们可能需要写一些私密的博客，通过密码验证的方式让人不能随意浏览。
* 这在wordpress，emlog或其他博客系统中都很容易实现，然而hexo除外。:(
* 为了解决这个问题，我们需要安装“ hexo-blog-encrypt”扩展。
### 安装指令
```bash
npm install hexo-blog-encrypt
```
### 快速使用
将 password 标签添加到您的 Front-matter 里，就像这样：
```bash
---
title: xxx
date: 2022-08-26 17:34:59
password: 233
tags: xxx
---
```
### 特性
* 一旦你输入了正确的密码，它就会被存储在本地浏览器的localStorage中。按个按钮，密码将会被清空。若博客中有脚本，它将被正确地执行。
* 支持按标签加密。
* 所有的核心功能都是由原生的API所提供的。在Node.js中，我们使用Crypto。在浏览器中，我们使用Web Crypto API。
* PBKDF2，SHA256被用作复制密钥，AES256-CBC被用作加解密，我们还使用HMAC来验证密文的来源，并确保其纠正。
* 广泛地使用Promise来进行异步操作，从而确保线程不被杜塞。
* 过时的浏览器将无法正常显示，因此，请升级您的浏览器。

## other
如果你不想每次新建文章都要自己写的话，那么就修改`./scaffolds/post.md`文件。

# 博客加速
> 于2022年11月测试vercel.app已经被墙了，但是还可以通过自备域名的方式。

使用Vercel加速Hexo博客访问速度。
如果你想让您的博客更快的更新，那么可以采取这种方法的同时，也可以加速你的博客。
该方法适用于已经使用我的方法将博客源文件备份到了github的。
## 步骤
1. 打开vercel.com,这个域名没有被墙。登陆的时候选择github方式。
   ![](/img/hexoplugin/20221116141135.png)  
2. 创建新项目，选择 git 仓库中已存在的项目，然后选Deploy。
3. 创建完成后就可以通过 `你项目名称.vercel.app` 访问了。但是目前vercel.app已经被墙了，所以还需要在设置里添加自己的域名。
4. 切换到自己的域名控制台里添加一条cname记录，指向这个地址 `cname-china.vercel-dns.com` 。
5. 将Git Branch里的内容改为存放自己博客备份的分支，比如我的就是backup分支。同时也可以将分配给你的二级域名也改成这个分支。
   ![](/img/hexoplugin/20221116144428.png)  
6. 切换到 Git 项目，将 Production Branch 也更改为 backup 分支。
   ![](/img/hexoplugin/20221116144502.png)  
7. 此时如果你的 backup 分支如果有更新，那么 vercel 会自动克隆你的源文件然后编译更新cache。
8. 使用这种方法独立于github page。只有当你 hexo d 之后源博客才会更新。
## 保护博客源码内容
如果你认为这种方式要更好一些，当然你也可以选择直接关掉github page并将仓库设置为private，这样别人就无法轻易获取您博客的源代码了。

# 写作流程
## 第一次使用
1. 安装nodejs，git，hexo，npm
2. git clone 仓库链接
3. cd到目录然后 cnpm install
4. 撰写博客
## 通过github page
1. 撰写博客
2. 保存到github。
```bash
// 保存所有文件到暂存区
git add .
// 提交变更
git commit -m 'Backup'
// 推送到github，分支为Backup
git push origin backup
```
3. 生成并提交网页。
```bash
// 生成静态网页
hexo g
// 生成目录文件
hexo algolia
// 打开本地服务器预览
hexo s
// 提交到github page
hexo d
```
4. 等待网页更新。

## 通过vercel.com
1. 撰写博客
2. 保存到github。
```bash
// 保存所有文件到暂存区
git add .
// 提交变更
git commit -m 'Backup'
// 推送到github，分支为Backup
git push origin backup
```
3. 等待网页更新。


# 测试
```java java
import java.util.Scanner;
...
Scanner in = new Scanner (System.in);
// 输入 Scan 之后，按下键盘 Alt + “/” 键，Eclipse 下自动补全。
System.out.println (in.nextLine ());
System.out.println ("Hello" + "world.");
```

原本用于日语汉字假名注音，但别的注音也可以。
为了兼容性，采用 markdown-it-ruby 的基本格式： {文字^注音} ，并且为了兼容表格，将分隔符由 | 换成了 ^ 。
注音分隔基于 furigana-markdown-it 显示说明看这里

{取り返す^とりかえす}	取とり返かえす
{可愛い犬^か+わい・い・いぬ}	可愛かわいい犬いぬ
{可愛い犬^か・わい・いいぬ}	可か愛わいい犬いぬ
{アクセラレータ^accelerator}	アクセラレータaccelerator
{accelerator^アクセラレータ}	acceleratorアクセラレータ
{食べる^たべる}	食たべる
{食べる^=たべる}	食べるたべる
{あいうえお^*}	あ●い●う●え●お●
{あいうえお^*❤}	あ❤い❤う❤え❤お❤
{常用账号^contact}	常用账号contact
