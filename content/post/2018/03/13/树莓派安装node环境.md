---
title: "树莓派安装node环境"
date: 2018-03-13T14:21:50+08:00
tags: ["嵌入式","树莓派","Node"]
categories: ["嵌入式","树莓派","Node"]
toc: false
---

# 树莓派安装node环境

![https://oss.lucoder.com/uploads/2018/03/13/201803130815.png](https://oss.lucoder.com/uploads/2018/03/13/201803130815.png)

## 下载node包
> https://npm.taobao.org/mirrors/node/

我选择的是`node-v9.5.0-linux-armv7l.tar.gz`，当然可以选择其他版本，但是注意是`arm版本`即可。

```code
wget https://npm.taobao.org/mirrors/node/v9.5.0/node-v9.5.0-linux-armv7l.tar.gz
```

## 解压node包

```code
tar -zxvf node-v9.5.0-linux-armv7l.tar.gz
```

## 安装

复制到`/usr/`目录里面

```code
sudo cp -rvi node-v9.5.0-linux-armv7l /usr/
```

## 添加链接

首先切换到`/usr/bin`目录。
```code
sudo ln -s ../node-v9.5.0-linux-armv7l/lib/node_modules/npm/bin/npm-cli.js
```

## 测试结果
```code
pi@raspberrypi:~ $ npm -v
5.6.0
```
