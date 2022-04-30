---
title: Linux(Debian)服务器使用Docker简单搭建WordPress服务器
date: 2022-04-30 16:17:33
tags: [linux, 服务器, docker, WordPress]
---

Docker可以简化WordPress的部署过程，不需要自己安装phpMyAdmin，也不需要自己配置MySQL数据库。

仅简单的、基本的安装，诸如HTTPS配置、SSL证书配置等没有涉及（我也没有机会涉及）。

## 安装Docker

不清楚包管理工具是否有正常的Docker可以安装，Debian中APT的Docker不到100KB，看起来并不能用。因此，采用菜鸟教程提供的方法：

使用官方安装脚本自动安装
```sh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

也可以使用国内 daocloud 一键安装命令：
```sh
curl -sSL https://get.daocloud.io/docker | sh
```

## 下载Docker镜像

使用docker pull拉取WordPress和MySQL镜像：

```sh
docker pull wordpress
docker pull mysql
#应该并不需要:latest
#如docker pull wordpress:latest
```

如果你要安装特定版本，则名字改为wordpress:版本号。

## 创建一个网络（两容器可互相访问的空间）

使用--link链接WordPress和MySQL两个容器的方法已经过时，并且不久的将来会弃用，因此使用docker network网络。

创建一个名为wp-net的网络，后续运行容器加入这个网络中。

```sh
docker network create wp-net
```

## 运行容器

#### 首先是运行MySQL：

```sh
docker run --name mysql --network wp-net --network-alias db -e MYSQL_ROOT_PASSWORD=你的密码 -d mysql
```

>--name：把这个容器命名为mysql  
--network：让这个容器加入名为wp-net的网络（重要）  
--network-alias：为这个容器创建别名db，理解为后面用这个名字代表数据库的地址（重要）  
--e：为设置环境变量，MYSQL_ROOT_PASSWORD指定MySQL中root用户的密码，经测试不指定无法正常启动容器。  
-d：后台运行

最后的mysql为镜像的名称，如果你要使用特定版本，则写为 mysql:5.1 即可。

#### 然后是运行WordPress：

```sh
docker run --name wp --network wp-net -p 80:80 -d wordpress
```

部分参数同上一步，一定要加入MySQL所在的网络，否则无法访问。

>-p：将容器内的端口映射到宿主机上，像虚拟机的端口转发，像路由器的端口绑定。这里是把容器内的80端口（冒号后面）映射到主机的80端口（冒号前面）。

## 配置安装WordPress

在浏览器中进入你的网站进行配置，直到数据库配置页。

你可能（至少我是）需要在MySQL中创建一个数据库，和WordPress的数据库名相同。

数据库的用户名注意为root，因为Docker创建的时候默认为root，除非你自己设定了其他名字。

数据库的地址（默认为localhost），为改为Docker运行MySQL时设定的--network-alias，我这里为db，除非你使用外部数据库。

## MySQL新建数据库

你可能会遇到WordPress报能够连接到数据库但数据库不存在的问题，正如我。

进入MySQL容器：（注意容器的名字）
```sh
docker exec -it mysql /bin/bash
```

 打开数据库：
```sh
mysql -u root -p
```

创建数据库：（自己决定数据库名称）
```sh
create database wordpress;
```

最后，quit退出MySQL环境，exit退出容器。

## 结语

作者并不熟悉Docker，只是记录一下WordPress的配置方法以备忘，很多Docker的术语可能不正确。
