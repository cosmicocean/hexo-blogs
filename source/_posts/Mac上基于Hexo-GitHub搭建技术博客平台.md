---
title: Mac上基于Hexo+GitHub搭建技术博客平台
date: 2017-02-14 16:40:57
tags:
---

## 系统环境 ##

- Mac 版本：macOS Sierra Version 10.12.2
- CPU: 3.1GHz Intel Core i7
- Memory: 16GB

## 安装软件 ## 

- 安装XCode
    - 作用：Hexo的编译需要XCode
    - 安装源：直接在[AppStore](https://itunes.apple.com/us/app/xcode/id497799835?ls=1&mt=12)下载安装即可
- 安装Node.js和npm
    - 作用：生成静态页面；Hexo基于Node.js
    - 安装源：safari打开[Node.js官网](https://nodejs.org/en/)下载macOS(x64)版本
    - 本文安装版本：*node-v6.9.5.pkg*
- 安装Git
    - 作用：将本地hexo内容提交到GitHub上
    - 安装源：安装XCode就自带Git
- 申请GitHub账号
    - 作用：博客的远程建库、域名、服务器等；需要与本地hexo建立连结
- 安装Hexo
    - 作用：简单、快速、强大的博客框架
    - 安装方法：

```
$ sudo npm install -g hexo-cli
```

## 搭建博客网站 ##

- 创建博客网站并初始化

```
$ hexo init <folder>    —— 创建新的博客网站
$ cd <folder>           —— 进入新创建的博客目录
$ npm install           —— 初始化博客网站
```
    
<folder>就是你的博客根目录，该博客的所有操作都在里面进行。

例如，我将要搭建*hexo-blogs*的博客，实际执行等命令为


```
$ hexo init hexo-blogs
$ cd hexo-blogs
$ npm install
```

后续操作均以博客目录*hexo-blogs*为例进行讲解。

- 启动博客

```
$ hexo server
```

打开浏览器，通过地址：[http://localhost:4000](http://localhost:4000)，即可访问刚刚搭建的博客网站。

- 停止博客

```
Ctrl+C
```

## 配置博客网站 ## 

博客的默认配置文件是_config.yml，但是也可以采用单独等自定义配置文件。本文以默认配置文件_config.yml为例进行讲解。

- 修改_config.yml内容，例如：

```
title: 「南北东篱」的技术博客
subtitle: 不惜南辕北辙，誓要采菊东篱，终现南山
description: 孜孜攀登技术山峰
author: ndbl(王志云)
language: en
```

- 重新启动博客

```
$ hexo server
```

打开浏览器，通过地址：[http://localhost:4000](http://localhost:4000)，即可访问配置更新后的博客网站。

## 创建博客 ##

- 创建新的博客文章

```
hexo new demo
```

将在*hexo-blogs*下生成*source/_posts/demo.md*文件，编辑该文件即编辑了博客文章的内容。

- 生成静态文件

```
hexo generate
```

- 发布博客文章

```
hexo deploy
```

打开浏览器，通过地址：[http://localhost:4000](http://localhost:4000)，即可访问到新建的博客文章*demo*。

## 关联至GitHub ##

- 在GitHub上创建博客仓库

在GitHub的个人主页上，点击右上角的*new repository*，其中*Repository Name*填写为:
    
```
<用户名>.github.io.git
```

例如，笔者的库名为：
    

```
cosmicocean.github.io.git
```


- 安装Git部署插件：*hexo-deployer-git*

```
$ npm install hexo-deployer-git --save
```

- 修改配置文件 （_config.yml或者custom.yml）

打开custom.yml文件，修改其中的 deploy 配置项如下

```
deploy: 
  type: git 
  repo: https://github.com/用户名/用户名.github.io.git 
  branch: master
```

例如，笔者的配置为

```
deploy: 
  type: git 
  repo: https://github.com/cosmicocean/cosmicocean.github.io.git 
  branch: master
```

需要注意的是：冒号后面有一个空格；使用github可以不用写branch那一行。

如果要使用多个 deployer，可改成如下样式：

```
deploy:
- type: git
  repo:
- type: heroku 
  repo:
```

## 发布到GitHub ##

输入命令

```
hexo deploy
```

成功后，通过网址：[https://<用户名>.github.io/](https://<用户名>.github.io/)即可访问正式发布后的博客。

例如，笔者的博客地址为：[https://cosmicocean.github.io/](https://cosmicocean.github.io/)


以后每次执行就可以依次输入下面三行命令更新发布：

```
hexo clean
hexo generate
hexo deploy
```

## 日常步骤总结 ##

一旦搭建好博客网站，日常写博客的流程总结为

- 创建博客文章
        
```
$ hexo new <article_name>
```
    
编辑source/_posts/<article_name>.md的内容（markdown格式）

- 全新生成静态文件

```
$ hexo clean
$ hexo generate
```

如果希望服务能动态监视文件的变化自动生成静态文件，可以使用如下命令代替上面的两个命令

$ hexo generate --watch

- 本地测试发布

若本地服务没有停掉，无需重新启动；若本地服务已经通过`Ctrl+C`停掉了，可以通过下面的命令启动

```
$ hexo server
```

访问：[http://localhost:4000](http://localhost:4000)验证内容是否正确。
    
- 正式发布到网站

```
$ hexo deploy
```

访问：[https://<用户名>.github.io/](https://<用户名>.github.io/)验证内容是否正确。

## 参考资料 ##

- [Hexo Docs](https://hexo.io/docs/)：本文依据的主要文档
- [HEXO+Github,搭建属于自己的博客](http://www.jianshu.com/p/465830080ea9)：具体安装步骤参考
- [在Mac下通过HEXO在Github上搭建博客](http://www.jianshu.com/p/ecd51e8ef2fa)：具体安装步骤参考
- [Node.js](https://nodejs.org/en/)：Node.js官方网站及下载地址
- [Mac上安装Node和NPM](http://www.jianshu.com/p/20ea93641bda)：Node和NPM等安装方法参考文档
