---
title: "伏羲之光项目"
date: 2018-12-03T10:01:23+08:00
draft: false
tags: ["开源项目","牧轩项目","光学计算"]
categories: ["Github"]

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
# comment: false
toc: true
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
# contentCopyright: false
# reward: false
# mathjax: false
---

# 项目介绍

伏羲之光是一个基于Opencv+GDAL的图像处理框架支持nuget下载包，采用ASP.NET Core 2.1 开发程序，可以生成跨平台可执行程序。

# 最初的幻想

我们希望打造一个跨平台图片处理框架：

 1. 采用.NET Standard开发，基于Opencv+GDAL；
 2. 使用ASP.NET Core 开发，跨平台能力强大，同时支持Windows、Linux、Mac、ARM(仅对树莓派和Jetson做了测试)等任何支持ASP.NET Core的任意发行版；
 3. 提供GDAL操作接口，支持多光谱遥感图像的处理；
 4. 遵守GPL开源协议，所有的用户修改二次开发必须遵守GPL许可证；

# 结构

```
|--Fucier
|----Video //视频处理命名空间
|--------> VideoGrapper
|----GDAL //GDAL操作命名空间
|-------> GeoSplitor	//将多光谱图像波段拆分
|----Extension //扩展中心
```

# 开源

代码采用GPL开源协议开源，有两个仓库地址：

国内： https://gitee.com/muxhub/fucier （主源）
国外： https://github.com/muxhub/fucier （同步源）

# 使用资费

开放研究机构和日常免费使用所有的产品，禁止用于商业活动。

# 团队

牧轩开发团队：https://dev.lucoder.com

# 如何使用

```shell
# 稳定版
git clone https://gitee.com/muxhub/fucier
#开发板
git clone -b dev https://gitee.com/muxhub/fucier
```

# 编译

由于程序采用C#基于ASP.NET Core开发所以必须拥有ASP.NET Core的运行时或者SDK（如果需要编译修改代码）。

# 加入我们

联系邮箱：support@lucoder.com

# 联系我们

官网：https://open.lucoder.com/fucier
邮箱：support@lucoder.com