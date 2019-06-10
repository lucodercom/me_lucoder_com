---
title: "JetsonTx2刷机（瑞泰版）"
date: 2018-07-25T14:21:50+08:00
tags: ["嵌入式","Jetson TX2","刷机"]
categories: ["嵌入式","Jetson TX2","刷机"]
toc: false
---

# 下载Jetson

首先去`Nvidia`官网下载`Jetsonpack`，我用的`Jetpack 3.1`（[下载地址](https://developer.nvidia.com/embedded/jetpack)）
<!--more-->
# 下载必要文件

用普通用户执行，如果中间需要请求权限，它会提示你下载的文件。
![https://oss.lucoder.com/uploads/2018/07/25/20180725121427.png](https://oss.lucoder.com/uploads/2018/07/25/20180725121427.png)

然后下一步展开Jetpack：

![https://oss.lucoder.com/uploads/2018/07/25/20180725121446.png](https://oss.lucoder.com/uploads/2018/07/25/20180725121446.png)

点击下一步，选择你需要的板子包：

![https://oss.lucoder.com/uploads/2018/07/25/20180725121503.png](https://oss.lucoder.com/uploads/2018/07/25/20180725121503.png)

之后可能会请求`root`权限：

![https://oss.lucoder.com/uploads/2018/07/25/20180725121527.png](https://oss.lucoder.com/uploads/2018/07/25/20180725121527.png)

下载你需要的数据包，一般默认即可，如果有需求可以点击左上角选择。

![https://oss.lucoder.com/uploads/2018/07/25/20180725121757.png](https://oss.lucoder.com/uploads/2018/07/25/20180725121757.png)

上一个步骤将会持续很长时间，尤其是你的网络如果不好那就放倒实验室过夜吧。

![https://oss.lucoder.com/uploads/2018/07/25/20180725121817.png](https://oss.lucoder.com/uploads/2018/07/25/20180725121817.png)

好了，如果你是瑞泰的板子，那么到此就可以停下来了，如果是原生的板子，请移步这里[https://v.qq.com/x/page/b0515967lbr.html](https://v.qq.com/x/page/b0515967lbr.html)，根据国外的`光头哥`步骤一步一步来。

![https://oss.lucoder.com/uploads/2018/07/25/20180725122009.png](https://oss.lucoder.com/uploads/2018/07/25/20180725122009.png)

# 开始刷机

瑞泰的板子你可能需要`BSD`，我这里提供一份，[点击这里]()下载。然后根据`BSD`的说明一步一步来就行。

下面按照下面的图链接你的板子和硬件设备：

![https://oss.lucoder.com/uploads/2018/07/25/20180725120342.png](https://oss.lucoder.com/uploads/2018/07/25/20180725120342.png)

一定注意图中的`短接口`，你需要用一个导线让这两个短路接通，这样板子通电启动将会进入`recovery`模式，然后才能进行刷机，否则必然刷不上！

然后进入到你的Jetpack目录，刷机即可。
```
cd <Jetpack_installed_dir>/64_TX2/Linux_for_tegra_tx2/

./flash.sh
```
建议使用下面的这个，因为瑞泰的默认没有刷入USB3功能，使用下面指令可以刷入USB3功能。
```
./flsh.sh astro-usb3 mmcblk0p1
```

刷机持续时间很长，可以出去散散步，喝杯水，大概持续10分钟刷机完成。途中风扇会一直转不用但心。