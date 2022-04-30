---
title: 基于HEXO在GITHUB PAGE建立简单博客
date: 2022-04-30 18:04:18
categories: [技术]
tags: [HEXO, 博客, Github]
---

## 前言

如果你不想折腾，那么托管WordPress是最好的选择。

HEXO不止可以部署到Github Pages，因为其本质是通过Markdown生成静态页面，所以任何你想到的网页容器都行。

WordPress虽然一站式解决，但是安装很麻烦，故我选择了HEXO。

## 环境

HEXO基于Node.js，建议使用nvm（或nvm-windows）安装，方便管理各种版本的node.js。

在nvm中安装管理node.js版本请看别的教程。

然后安装HEXO：
```sh
npm install -g hexo-cli
```

## 开始

到达你要放置博客的文件夹，通过
```sh
hexo init
```
建立博客的“脚手架”，当让也可以
```sh
hexo init new_dir
```
创建一个文件夹并建立“脚手架”。

## 配置

博客根目录下的_config.yml有博客的基本设置，如标题、主题等。

详细的配置，还是看[HEXO官方文档](https://hexo.io/zh-cn/docs/configuration)比较方便。

## 主题

虽然许多主题作者都开始转向使用npm来分发主题，但这里还是建议手动解压主题文件到themes文件夹。这样一来问题会少一些。

一般而言，你只需要下载主题压缩包，解压到themes/，然后在博客根目录新建或重命名一个_config.主题名称.yml文件，最后更改_config.yml中的theme即可。

通常_config.主题名称.yml文件是对主题额外的东西的设置。

热门的主题都有对应的详尽的安装方法。

## 撰写

可以通过
```sh
hexo new post[/draft/page] '新的标题'
```
来创建新的文件，新的文件在博客根目录下的source/_posts，你也可以自己创建。

一个新文件的头部通常是这样的：
```txt
---
title: 文章标题
date: 时间，如 2021-08-08 21:27:14
tags: [标签, 标签数组的第二个, 等等等等等等]
categories: [分类, 子分类]
---
```

## 发布

这里只介绍使用GITHUB PAGES的方法，即HEXO文档中的[Git部署](https://hexo.io/zh-cn/docs/one-command-deployment#Git)方法。

需要安装依赖：
```sh
npm install hexo-deployer-git --save
```
>--save、-S参数意思是把模块的版本信息保存到dependencies（生产环境依赖）中，即你的package.json文件的dependencies字段中

然后编辑_config.yml，找到deploy项，改成如下：
```txt
deploy:
  type: 'git'
  repo: 你的github网页地址，是浏览器中的，如https://github.com/Sodacooky/sodacooky.github.io
  branch: gh-pages
  message: deploying
```

其中，branch为push的分支，【一定】不要是本来存放博客内容的“脚手架”的分支，deploy将会把并且只会把编译（生成）好的前端静态页面push到这个分支。

还要在Github仓库中，在Settings -> Pages -> Source中把Branch改为你上面设置的分支。

最后，在你通过```hexo new```创建文件，```hexo clean```清理生成缓存（可选），```hexo generate```生成静态文件后，你就可以
```sh
hexo deploy
```
将更新部署到Github，等待一下就生效。

## CNAME

就在上面设置分支时，你会看到一个Custom domain，如果你直接在那里添加，只会在远程仓库中的指定分支中创建一个CNAME文件，下次push就会失效。

你应该在博客根目录的source文件夹中，放入这个CNAME文件，内容是你的域名。

记得先在DNS添加CNAME记录。