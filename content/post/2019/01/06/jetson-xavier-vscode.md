---
title: "Jetson TX2 编译安装Visual Studio Code"
date: 2019-01-06T21:01:23+08:00
draft: false
tags: ["开发", "嵌入式", "Xavier","Visual Studio Code"]
categories: ["开发", "嵌入式"]
toc: false
---

VSCode是微软开发的一款开源的编辑器，支持Linux、Windows、Mac等平台，鉴于笔者是在Jetson Xavier开发的，所以今天编译一份arm平台的安装包。

# 先说坑

1. 不要使用最新版nodejs

![https://oss.lucoder.com/uploads/2019/01/06/20190106065826.png](https://oss.lucoder.com/uploads/2019/01/06/20190106065826.png)

2.强外软件
需要强外下载一个包，如果你没办法出墙，下面的也不用看了。

3. 安装一个Ubuntu 的几个包libxkbfile-dev,libsecret-1-dev

![https://oss.lucoder.com/uploads/2019/01/06/20190106075149.png](https://oss.lucoder.com/uploads/2019/01/06/20190106075149.png)

![https://oss.lucoder.com/uploads/2019/01/06/20190106075149.png](https://oss.lucoder.com/uploads/2019/01/06/20190106075149.png)

```shell
sudo apt-get install qemu qemu-user-static debootstrap gcc-arm-linux-gnueabihf g++-arm-linux-gnueabihf

sudo chroot rootfs apt-get install -y libx11-dev
```

# 安装环境

Vscode 是一个基于NODE JS开发的一款跨平台IDE，所以第一步是安装NODE环境，首先确定你的版本，输入uname -a查看系统的版本信息。如图所示：

![https://oss.lucoder.com/uploads/2019/01/06/20190106061827.png](https://oss.lucoder.com/uploads/2019/01/06/20190106061827.png)


可以看出，我的系统是aarch64,即64位的系统，所以下载arm V8版本。知道了上面的坑之后，这里下载就要注意了，不要下载最新的！

```shell
wget https://npm.taobao.org/mirrors/node/latest-v8.x/node-v8.9.4-linux-arm64.tar.gz
```

![https://oss.lucoder.com/uploads/2019/01/06/20190106062135.png](https://oss.lucoder.com/uploads/2019/01/06/20190106062135.png)


解压安装软件

![https://oss.lucoder.com/uploads/2019/01/06/20190106062414.png](https://oss.lucoder.com/uploads/2019/01/06/20190106062414.png)


安装软件(注意，本人的习惯所有的环境都安装在了/usr/local里面，根据自己的需要修改)

```shell
mv node-v10.15.0-linux-arm64 node

sudo mv node /usr/local/
```

配置环境经变量

```shell
gedit ~/.bashrc

source ~/.bashrc

node -v
```

![https://oss.lucoder.com/uploads/2019/01/06/20190106062859.png](https://oss.lucoder.com/uploads/2019/01/06/20190106062859.png)


# 下载VSCODE源代码

你可以从下面地址直接下载源码，也可以用下面的方法克隆代码。

```shell
git clone https://github.com/microsoft/vscode
cd vscode
```

# 安装Yarn

这里选择node的方式，`因为我们只是用来编译vscode，并不做生产环境`，生产环境请勿使用node 版本，具体原因请查询相关资料。

首先去安装淘宝npm镜像

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

然后安装Yarn

```shell
npm install --global yarn
```

![https://oss.lucoder.com/uploads/2019/01/06/20190106065517.png](https://oss.lucoder.com/uploads/2019/01/06/20190106065517.png)

查看是否安装成功
```
yarn --version
```
![https://oss.lucoder.com/uploads/2019/01/06/20190106065710.png](https://oss.lucoder.com/uploads/2019/01/06/20190106065710.png)

# 安装VSCODE

安装VSCOde之前需要先安装一个依赖环境，如果已经装过可以跳过

```shell
sudo apt-get install libx11-dev
```

导入配置

```shell
export CC=$(which arm-linux-gnueabihf-gcc)
export CXX="$(which arm-linux-gnueabihf-g++) -L$(pwd)/rootfs/usr/lib/arm-linux-gnueabihf/"
export npm_config_arch=arm
```

### 第一种方式

安装脚本

```shell
./scripts/npm.sh install --arch=arm64
```

然后安装core

```shell
./scripts/code.sh
```

### 第二种方式

```shell
yarn
yarn run gulp vscode-linux-arm-min
yarn run gulp vscode-linux-arm-build-deb
```

安装之前建议使用最强模式

```shell
sudo nvpmodel -m 0
```
这时候会使用8核并行编译源码，速度会比较快，但是耗能明显增加，在安装结束之后可以重新修改回原先的模式或者适合自己使用的模式。

编译过程是一个很美丽的过程，同时你可以看出来为什么要你开启最强模式。

![https://oss.lucoder.com/uploads/2019/01/06/20190106074330.png](https://oss.lucoder.com/uploads/2019/01/06/20190106074330.png)

![https://oss.lucoder.com/uploads/2019/01/06/20190106080140.png](https://oss.lucoder.com/uploads/2019/01/06/20190106080140.png)

![https://oss.lucoder.com/uploads/2019/01/06/20190106081705.png](https://oss.lucoder.com/uploads/2019/01/06/20190106081705.png)

虽然编译的过程很紧张，但是结果不尽人意，我编译的code只有`armhf`的`deb`没有`arm64`的`deb`，换句话说只能通过脚本启动软件，不能编译成一个可以安装的`deb`，并且插件或许需要单独配置，因为我启动的软件插件不能使用的。