---
title: "Windows10使用Linux子系统"
date: 2018-07-22T14:21:50+08:00
tags: ["Windows","Linux","WSL"]
categories: ["Windows","Linux","WSL"]
toc: false
---

Windows 10 子系统出来已经很久了，但是笔者一直没有开始使用，因为当初用过一次很不稳定，功能也很少，但是最近有了Linux开发需求，所以被逼要不装双系统，要不虚拟机，综合了一下开始拾起Windows 10 子系统。

笔者使用的环境：

|环境 |参数
|---|---
|操作系统|Windows 10 Professional x64
|子系统 | Ubuntu 16.04



# 安装系统

安装系统很简单只需要点击Windows 10 应用商店，然后搜索Ubuntu，按照你的需求选择系统即可。

![https://oss.lucoder.com/uploads/2018/07/22/20180722202001.png](https://oss.lucoder.com/uploads/2018/07/22/20180722202001.png)

点击下载之后只需等待一会儿，玩会儿手机或者冲杯咖啡...

![https://oss.lucoder.com/uploads/2018/07/22/20180722202149.png](https://oss.lucoder.com/uploads/2018/07/22/20180722202149.png)

经过漫长的等待之后你就可以看到系统已经安装完毕。

![https://oss.lucoder.com/uploads/2018/07/22/20180722202435.png](https://oss.lucoder.com/uploads/2018/07/22/20180722202435.png)

# 设置系统

系统安装完成之后需要预先设置你的系统。

![https://oss.lucoder.com/uploads/2018/07/22/20180722203117.png](https://oss.lucoder.com/uploads/2018/07/22/20180722203117.png)

首先设置用户：

![https://oss.lucoder.com/uploads/2018/07/22/20180722203812.png](https://oss.lucoder.com/uploads/2018/07/22/20180722203812.png)


然后首选当然修改root的密码：
```shell
sudo passwd root
```

操作之后我们尽量选择一个比较好的源，当然Ubuntu默认的源也行，但是国内速度有点慢。建议添加清华大学的数据源：
```shell
sudo cp -rvf /etc/apt/source.list  /etc/apt/source.list.bak
sudo vim /etc/apt/source.list
```
清华大学源，我选择的是Ubuntu 16.04 LTS：
```source
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse

```
更新数据源：
```shell
sudo apt-get update
```
# 文件和目录

根目录：
```
C:\Users\xxx\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu16.04onWindows_79rhkp1fndgsc\LocalState
```
Home文件夹：
```
$rootfs/home/xxx
```

# 可能出现的错误

![https://oss.lucoder.com/uploads/2018/07/22/20180722202501.png](https://oss.lucoder.com/uploads/2018/07/22/20180722202501.png)

#### 解决办法：

首先管理员方式启动Powershell，然后输入下面的指令：
```shell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```
输入结束了然后输入`y`，电脑重启了！！！重启结束就可以正常进入系统了！