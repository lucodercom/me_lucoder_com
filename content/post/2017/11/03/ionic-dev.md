---
title: "IONIC开发环境搭建"
date: 2017-11-03T16:01:23+08:00
draft: false
tags: ["移动开发","混合开发技术","IONIC"]
categories: ["移动开发","混合开发技术","IONC"]
toc: false
---
# IONI开发环境搭建
> 官网：[https://ionicframework.com/docs/](https://ionicframework.com/docs/)

ionic是一个用来开发混合手机应用的，开源的，免费的代码库。可以优化html、css和js的性能，构建高效的应用程序，而且还可以用于构建Sass和AngularJS的优化。ionic会是一个可以信赖的框架。

## 预备环境

在nodejs官网[http://nodejs.cn/](http://nodejs.cn/)下载最新的适合电脑系统要求的nodejs安装包，解压文件配置环境变量，如：`NODE_HOME`：`D:\DevT\node-v8.9.3-win-x64`，配置完毕之后可以使用命令查看是否安装完成。
```shell
> node -v
v8.9.3
> npm -v
5.5.1
```
## 安装IONIC

完成上述步骤之后，你可以安装ionic了，使用npm安装ionic。
```shell
> npm install -g ionic
> ionic -v
3.20.0
```

## cordova 环境

```shell
> npm install cordova -g cordova
> cordova -v
6.5.0
```
此时ionic开发环境已经完全安装完毕，`ionic start [AppName]` 开始创建ionic项目