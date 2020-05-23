---
title: Hexo搭博客
date: 2017-01-01
categories:
	- Git
---
## 前言
Hexo是一款快速、简单、并且强大的博客博客模板框架 - 基于nodejs。特点:基于nodejs、使用Markdown书写文章、无需数据库、可以使用GitHub Pages发布。

## 搭建环境
### Node.js
- linux:
``` bash
$ sudo apt-get install nodejs
$ sudo apt-get install npm
```
- windows:
Node.js官网下载安装即可

### Hexo
- linux:
``` bash
$  sudo npm install hexo-cli -g
```
- windows:
``` bash
npm install hexo-cli -g
```

### Git(一个github账号是必备的)
- linux:
``` bash
$ apt-get install git-core
```
- windows:
github官网下载即可

## 搭建步骤

### 配置Hexo项目
- 首先创建一个Hexo项目：

``` bash
$ hexo new <project>
```

- 然后找到hexo项目里的_config.yml文件找到并修改以下地方：
 之后部署不需要输入git账号密码的方式

``` bash
deploy:
  type: git
  repo: git@github.com:username/user_name.github.io.git
  branch: master
```
- 需要输入git账号密码部署的方式:

``` bash
deploy: 
  type: git
  repository: http://github.com/huangjunhui/huangjunhui.github.io.git
  branch: master
```

### 配置Git
- 进入github，创建一个项目，命名方式：user_name.github.io
- 进入Git bash，生成public key：
``` bash
ssh-keygen -t rsa -C "your_email@youremail.com"
```
- 然后会在控制台默认路径(C:\Users\Administrator)下生成.ssh文件夹，打开公匙保存文件，将其复制到github设置的SSH key里。
- 验证是否配置SSH key成功：
``` bash
$ ssh -T username@github.com
```
- 将本地仓库传到github上去：
``` bash
$ git config --global user.name "user_name"  
$ git config --global user.email "your_email@youremail.com"
```
- 部署博客
用Hexo搭好后，通过Git远程部署到Github Page上：
``` bash
hexo g
hexo d
```
- 本地打开调试，默认是4000端口：
``` bash
hexo server
```
- 出现显示信息与实际不符。Eg:文章数、分类等显示错误时。最好每次部署的时候就清理一次缓存：
``` bash
hexo clean
```

### 踩坑
- 可能会出现以下报错的情况：
``` bash
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
//解决办法
npm install hexo --no-optional
```