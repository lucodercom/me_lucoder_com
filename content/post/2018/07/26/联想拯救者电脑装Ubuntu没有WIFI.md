---
title: "联想拯救者电脑装Ubuntu没有WIFI"
date: 2018-07-26T14:21:50+08:00
tags: ["外星人","Ubuntu","WIFI"]
categories: ["外星人","Ubuntu","WIFI"]
toc: false
---

> 最近给联想电脑装Ubuntu系统，但是装完之后总是无法启动WIFI，而宽带上网却可以，给出一个解决办法，但是该办法应该只适合联想电脑，其他电脑请自测！

<!-- more-->

![https://oss.lucoder.com/uploads/2018/07/26/20180726190211.jpg](https://oss.lucoder.com/uploads/2018/07/26/20180726190211.jpg)

打开终端，输入下面指令：
```shell
sudo modprobe -r ideapad_laptop
```
一般的如果该方法有效，那么命令执行成功左上角就可以使用WiFi了。

#sudo gedit /etc/modprobe.d/wifiblacklist.conf

Linux系统特性，`一切皆配置`，打开文件后在第一行输入
```shell
blacklist ideapad_laptop
```
保存并关闭后再执行
```shell
#sudo modprobe -r ideapad_laptop
```
或者重启电脑

> TIPS: 新问题，检查是否使用键盘关闭了WIFI功能