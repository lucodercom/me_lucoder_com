---
title: "MS-MPI安装配置"
date: 2019-03-28T19:37:28+08:00
draft: false
tags: ["开发", "高性能计算", "MPI"]
categories: ["开发", "高性能计算", "MPI"]
toc: false
---

# MPI简介

# 官网

[http://www.mpich.org/downloads/](http://www.mpich.org/downloads/)

# 安装步骤

![打开官网](https://oss.lucoder.com/md/2019/03/28/20190328192844.png)

选择MS-MPI：

![选择MSMPI](https://oss.lucoder.com/md/2019/03/28/20190328192946.png)

![打开MSMPI](https://oss.lucoder.com/md/2019/03/28/20190328193113.png)

![下载MSMPI](https://oss.lucoder.com/md/2019/03/28/20190328193233.png)

![下载MSMPI](https://oss.lucoder.com/md/2019/03/28/20190328193302.png)

下载结束后，按照下一步下一步安装软件，安装结束后就可以配置环境变量进行编程了，假设你的安装目录是：C:\Program Files\Microsoft MPI(运行目录)和C:\Program Files (x86)\Microsoft SDKs\MPI（SDK目录）。

# 编程

创建一个C++项目：

![创建C++项目](https://oss.lucoder.com/md/2019/03/28/20190328193614.png)

右键点击项目，选择属性：

![设置属性](https://oss.lucoder.com/md/2019/03/28/20190328193728.png)

设置你的Include目录和Lib目录。（例如：我的Include目录--> C:\Program Files (x86)\Microsoft SDKs\MPI\Include，C:\Program Files (x86)\Microsoft SDKs\MPI\Include\x64 , 我的Lib目录--> C:\Program Files (x86)\Microsoft SDKs\MPI\Lib\x64）

![设置项目](https://oss.lucoder.com/md/2019/03/28/20190328193924.png)

主库必须选择：`msmpi.lib`

![填写库](https://oss.lucoder.com/md/2019/03/28/20190328194110.png)

注意一点：因为我全程配置的都是`x64`，所以选择项目也需要是`x64`的，如果你是`x86`的，俺就需要配置`x86`.

![最后一步](https://oss.lucoder.com/md/2019/03/28/20190328194148.png)

下面是一个mpi的案例：
```cpp
#include "pch.h"
#include <iostream>
#include <mpi.h>
#include<stdio.h>

int main(int argc, char *argv[]) {
	int totalTaskNum, rankID;

	int rt = MPI_Init(&argc, &argv);
	if (rt != MPI_SUCCESS) {
		printf("Error starting MPI.\n");
		MPI_Abort(MPI_COMM_WORLD, rt);
	}

	MPI_Comm_size(MPI_COMM_WORLD, &totalTaskNum);
	MPI_Comm_rank(MPI_COMM_WORLD, &rankID);

	printf("Hellow, world! %dth of totalTaskNum = %d\n", rankID, totalTaskNum);

	MPI_Finalize();

	return 0;
}
```

点击项目，重新生成：
![重新生成](https://oss.lucoder.com/md/2019/03/28/20190328194952.png)

# 运行

MPI的运行不能直接点击调试，需要使用命令运行，首先找到你的可执行程序，按住Shif键，点击鼠标右键，在Powershell打开（或者控制台打开）：

![重新生成](https://oss.lucoder.com/md/2019/03/28/20190328195133.png)

然后运行程序：

```shell
mpiexec -n 4 ConsoleApplication2.exe
```
![结果](https://oss.lucoder.com/md/2019/03/28/20190328195209.png)