---
title: "mpi并行计算环境搭建"
date: 2018-04-25T14:21:50+08:00
tags: ["高性能计算","并行计算","MPI"]
categories: ["高性能计算","并行计算","MPI"]
toc: false
---

# 什么是MPI

MPI是一个跨语言的通讯协议，用于编写并行计算机。支持点对点和广播。MPI是一个信息传递应用程序接口，包括协议和和语义说明，他们指明其如何在各种实现中发挥其特性。MPI的目标是高性能，大规模性，和可移植性。MPI在今天仍为高性能计算的主要模型。
<!--more-->
## 下载安装MPI

[下载地址](http://www.mpich.org/downloads/)

[Windows下载地址](https://msdn.microsoft.com/en-us/library/bb524831%28v=vs.85%29.aspx)

![Windows MPI](https://gitee.com/Gecore/muxuanuser.github.io/raw/master/content/post/201804/mpi%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97/1.png)

## 配置环境变量

安装之后配置环境变量，然后运行：
```shell
C:\Users\Lucifer>mpiexec
Launches an application on multiple hosts.

Usage:

    mpiexec [options] executable [args] [ : [options] exe [args] : ... ]
    mpiexec -configfile <file name>

Common options:

-n     <num_processes>
-env   <env_var_name> <env_var_value>
-wdir  <working_directory>
-hosts n host1 [m1] host2 [m2] ... hostn [mn]
-cores <num_cores_per_host>
-lines
-trace [filter] [Deprecated.  Please control MPI tracing with any Event Tracing
                  for Windows (ETW) tool. E.g. XPERF, LOGMAN]
-tracemax <size_in_mb> [Deprecated.]
-debug [0-3]

Examples:

    mpiexec -n 4 pi.exe
    mpiexec -hosts 1 server1 master : -n 8 worker

For a complete list of options, run mpiexec -help2
For a list of environment variables, run mpiexec -help3
```

出现上面的结果表示环境配置正确，开始下一步，否则检查自己的配置。

# MPI.NET

上面简介已经说明，MPI是一个跨语言通讯协议，不局限于某一中开发语言，由于笔者个人原因，选择了C#语言，所以使用MPI.NET。Java开发者可以借助于[OpenMPI执行Java MPI作业](https://blog.csdn.net/kongxx/article/details/52237416)这篇文章，有点麻烦，本文不在赘述。

## 创建一个Console 项目

然后使用`nuget`下载一个包`MPI.NET`，项目地址:[https://github.com/jmp75/MPI.NET](https://github.com/jmp75/MPI.NET)。

![下载mpi包](https://gitee.com/Gecore/muxuanuser.github.io/raw/master/content/post/201804/mpi%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97/2.png)

如果`nuget`下载包出现错误，可以使用这个压缩包文件，注意使用`.NET Fframework 4.0`，其他版本或许可用，但是有潜在的报错风险：[]()

## 测试代码

```C#
//初始化MPI运行环境  
using (new MPI.Environment(ref args))  
{  
    //获取Communicator  
    var comm = Communicator.world;  
    if (0 == comm.Rank)  
       {  
        //令0进程发送数据然后接收数据  
        comm.Send("Rosie", 1, 0);  
        // receive the final message   
        var msg = comm.Receive<string>(Communicator.anySource, 0);  
        //输出收到的信息  
        Console.WriteLine("Rank " + comm.Rank + " received message \"" + msg + "\".");  
        } 
        else 
        { 
            //令n程序接收上一进程发送的数据然后发给下一进程
            var msg = comm.Receive<string>(comm.Rank - 1, 0);
            //输入收到的消息
            Console.WriteLine("Rank " + comm.Rank + " received message \"" + msg + "\".");
            //发送给下一个进程 
            comm.Send(msg + ", " + comm.Rank, (comm.Rank + 1) % comm.Size, 0);
        }
}  
```

> 注意最后一行千万不要添加读取一行操作，否则必然报错！

创建一个脚本文件：
```bat
mpiexec -n 4 MpiSingleTestDemo.exe
pause();
```

![单机运行结果](https://gitee.com/Gecore/muxuanuser.github.io/raw/master/content/post/201804/mpi%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97/3.png)

## 多机运行

MPI是一个跨语言的通讯工具，我们可以用它来进行跨机器并行计算，需要借助的工具是`smpd.exe`，该文件在你安装MPI时候已经被自动安装。

我们仍然使用上述的例子进行下面的简单配置：

1. 寻找两台同一个网络下的电脑 A：10.12.52.160，电脑 B：10.12.37.191。

2. 两台电脑同时设置两个完全相同的账户和密码，假如均为 `root`和`123456`（Linux用户不要这么干）。

3. 关闭防火墙，尝试Ping主机，保证两台主机之间可以相互通讯。

4. 分别复制完全相同的软件到两台主机，并且保证位置相同，假如：`C:\mpi\`。

5. 双方主机启动smpd软件

```shell
smpd -d 2
```

类似下面的结果：

```
C:\Users\Lucifer>smpd -d 2
[-1:1260] Launching SMPD service.
[-1:1260] smpd listening on port 8677
```

6. 一方启动并行程序

```
mpiexec -hosts 2 10.12.52.160 3 10.12.37.191 4 MpiSingleTestDemo.exe
```

![多机运行结果](https://gitee.com/Gecore/muxuanuser.github.io/raw/master/content/post/201804/mpi%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97/4.png)


# 几点说明

MPI单机运行比较简单，但是多机运行时候务必保证下面几点：

1. 双方必须使用的是同一套用户名和密码，即用户名和密码完全相同。
2. 关闭windows防火墙，保证两台机器之间可以相互通讯。
3. 必须先启动smpd软件，这是两台计算机之间通讯必不可少的中介。
4. 并行程序必须每一个节点都有一套，并且程序的位置是相同的。