---
title: "Windows10系统和Ubuntu使用共享文件夹"
date: 2018-07-24T14:21:50+08:00
tags: ["Windows","Linux","共享文件"]
categories: ["Windows","Linux","共享文件"]
toc: false
---

# 需求

Ubuntu和Windows系统之间通过虚拟机交换文件相当不方便，如果是windows虚拟系统支持拖放文件，但是Windows10和虚拟机中的Ubuntu始终实现不了拖放文件，并且拖放文件有限制，如果是很大的文件（假如1G）拖放过程会很久。再者是用FTP方式，这种方式对于小文件和跨主机非常方便，但是现在显然不是最好的方案，所以我选择了共享文件夹。共享文件夹对于双方而言都是本地文件夹，访问速度非常快，并且不受文件大小的限制。
<!--more-->
# 着手

首先在Ubuntu关闭状态下，进行最初的设置，具体的就是`设置`->`共享文件夹`->`添加`->`配置`如图所示：

![https://oss.lucoder.com/uploads/2018/07/24/20180724114725.png](https://oss.lucoder.com/uploads/2018/07/24/20180724114725.png)

>假如你选择的文件夹名称为`vshare`

# Ubuntu挂载

下面就是Ubuntu挂载共享文件夹。

* 进入Ubuntu系统创建文件夹

```shell
sudo mkdir /mnt/vshare
```

* 设置文件夹权限

```shell
sudo chmod 755 /mnt/vshare
```

* 挂载文件夹

```shell
sudo mount -t vboxsf vshare /mnt/vshare/
```

* 测试

现在你已经可以正常的测试项目了，此时只需要在Window目录下面复制一些文件，然后进入Ubuntu刷新即可，同样的可以从Ubuntu复制文件放到Windows里面。

* 开机自动挂载

开启开机自动挂载功能。
```shell
sudo gedit /etc/fstab
```
在最后一行输入下面的文本。
```txt
vshare  /mnt/vshare vboxsf rw,gid=110,uid=1100,auto 0 0
```
保存，现在就已经实现了开机自动挂载功能，Ubuntu重启也会自动挂载，不用重复输入上面的命令。