---
layout: wiki
wiki: dvwa
breadcrumb: false
title: 安装DVWA
order: 2
comments: false
---
## Intro
具体如何安装DVWA我就不在过多赘述，直接使用Docker安装最为方便...

## 拉取DVWA镜像

1. 从Docker Hub上拉取DVWA镜像。运行以下命令：

```shell
docker pull vulnerables/web-dvwa
```

这将下载最新版本的DVWA镜像到本地。

2. 运行DVWA容器
下载完成后，我们可以通过运行DVWA容器来创建DVWA漏洞测试环境。使用以下命令：

```shell
docker run --rm -it -p 8083:80 vulnerables/web-dvwa
```

该命令将在本地启动一个DVWA容器，并将容器的80端口映射到主机的8083端口上。这样，我们可以通过访问`http://localhost:8083`来访问DVWA应用程序。

## 小结
> 看到没有，用docker安装就是这么简单。

## 登录DVWA并创建数据库

在浏览器中打开`http://localhost:8083`，您将看到DVWA登录页面。使用以下默认账号和密码登录：

![](/img/how2install/2023-06-21-19-35-56.png)

用户名：admin
密码：password

首次登录时，DVWA将提示您创建数据库。点击"Create / Reset Database"按钮，DVWA将自动创建所需的数据库。

![](/img/how2install/2023-06-21-19-36-53.png)

## 看到这个页面就算成功
![](/img/how2install/2023-06-21-19-34-28.png)

## 如何修改难度？
> 在主页找到这个东西点开你就知道了。。。
![](/img/how2install/2023-06-21-19-43-40.png)