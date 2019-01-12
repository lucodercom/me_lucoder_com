---
title: "Ionic错误集合"
date: 2018-07-02T14:21:50+08:00
tags: ["移动开发","IONIC"]
categories: ["移动开发","IONIC"]
toc: false
---

IONIC是一个非常强大的开源混合开发框架，但是部署项目总会遇到或多或少的错误，对于新手就会非常棘手。

# 安装错误


# 开发错误

## 缺少IONIC脚本

> ionic serve 编译在浏览器中预览项目时，提示报错 Error: Cannot find module '@ionic/app-scripts'。这个问题的主要现象就是创建的项目node_modules文件夹中没有任何文件

错误原因：网络原因或者版本不对应

解决办法：删除`NODE_MODULE`重新`npm install`即可。

## 缺少Python 和 Visual C++

> gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.

错误原因：编译`node-sass`需要使用到python 2.7(仓库已提供)和Visual C++(仓库已提供)。

解决办法：你可以下载仓库中的exe文件安装配置环境变量，但是更加建议你使用下面的命令，直接安装二进制文件，安装到全局node中，可以不安装Python和Visual C++。

```shell
cnpm install -g node-sass # 我用的淘宝的npm加速
```

# 编译错误


# 发布错误



