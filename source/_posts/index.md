---
title: 使用Hexo搭建自己的博客
date: 2020-07-21 09:33:14
categories: 博客搭建
tags: 博客搭建
---
# 使用Hexo搭建自己的博客

## 什么是Hexo？
> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

## 环境
- Mac OS
- Windows

## 安装前提
安装 Hexo 相当简单。然而在安装前，您必须检查电脑中是否已安装下列应用程序：
- [Node.js](https://nodejs.org/en/download/)：用来生成静态页面的
- [Git](https://git-scm.com/)：把本地的hexo内容提交到github上去
- [GitHub](https://github.com/)：博客部署到GitHub账户上

## 安装Hexo
这里呢，我们可以打开 Git Bash 命令行来进行Hexo的安装操作

1.建立一个存放Hexo的文件夹，例如 D:\Hexo，使用 Git Bash 进入的该文件夹下

```cmd
$ cd D:/Hexo
```

2.所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo

```cmd
$ npm install -g hexo-cli
```

3.安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件,即Hexo文件夹下会生成 blog 文件夹

```cmd
$ hexo init blog
$ cd blog
$ npm install
```

- 新建完成后，指定 blog 文件夹的目录如下：

```markdown
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

4.到这一步你的博客就已经搭建好了，我们可以输入命令下面，浏览器地址输入 http://localhost:4000 就可以本地预览啦

```cmd
$ hexo g
$ hexo server
```

## 配置GitHub

1.建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】，固定写法，同样也是你的博客网站

2.使用编辑器打开 blog 下的 _config.yml 文件

```
//修改如下字段：第二个字段是你的 GitHub 博客仓库地址
deploy:
   type: git
   repo: https://github.com/JamesGZM/JamesGZM.github.io
   branch: master
```

3.执行命令保存

```
$ npm install hexo-deployer-git --save
```

4.把项目部署提交到 GitHub 上去

```
$ hexo clean
$ hexo g
$ hexo deploy
```

## 预览查看

- 打开你的博客网站 ，我的是 https://1140691970.github.io ，你就会发现博客已经部署成功啦

## 相关配置
- 接下来的配置大家可以查看 [Hexo](https://hexo.io/zh-cn/docs/configuration.html) 的官方文档写的非常详细
- 关于主题配置官方推荐了很多，我这里使用的是 [butterfly](https://demo.jerryc.me/posts/21cfbf15/)
