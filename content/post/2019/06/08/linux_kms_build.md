---
title: "搭建Linux版KMS服务器"
date: 2019-06-08T14:24:33+08:00
draft: false
tags: ["开发", "Linux", "KMS"]
categories: ["开发", "Linux", "KMS"]
toc: false
---

采用的是vlmcsd的服务。

# 下载编译

```shell
git clone https://github.com/Wind4/vlmcsd
make -j
```

此时你可以看到有一个目录， bin 里面有两个文件一个是 vlmcsd 一个是 vlmcs。

# 运行软件

```shell
./vlmcsd
```

# 测试软件

```shell
./vlmcs
```

如果输出的是下面的结果表示成功了：
```shell
Connecting to 127.0.0.1:1688 ... 127.0.0.1:1688: Connection refused
Fatal: Could not connect to any KMS server
```