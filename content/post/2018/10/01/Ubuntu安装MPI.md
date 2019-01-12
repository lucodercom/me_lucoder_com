---
title: "Ubuntu安装MPI"
date: 2018-10-01T14:21:50+08:00
tags: ["高性能计算","并行计算","MPI"]
categories: ["高性能计算","并行计算","MPI","Ubuntu"]
toc: false
---

现正在研究ARM多机并行环境，由于学过MPI并行环境，而且MPI支持Ubuntu系统，所以就开始在Ubuntu上部署MPI开发环境。

![https://oss.lucoder.com/uploads/2018/10/01/20181020145010.png](https://oss.lucoder.com/uploads/2018/10/01/20181020145010.png)

# 下载源码

在 [下载地址](http://www.mpich.org/downloads/) 下载源码。

```shell
tar -zxvf mpich-3.2.1.tar.gz
cd mpich-3.2.1
```

![https://oss.lucoder.com/uploads/2018/10/01/2018102020145812.png](https://oss.lucoder.com/uploads/2018/10/01/2018102020145812.png)

# 配置编译

***此处有坑*** 先看`脱坑`.
```
./configure -prefix=/usr/local/mpich2
make -j8
sudo make install
```

[ ***NOTE*** ] : 根据自己的实际情况选择你使用的编译核心数目，我的4核机器，虚拟8核心，所以选择`-j8`，其二，因为现在我编译的是开发机，所以我就直接装到`/usr/local`里面，当然可以放到任何文件夹。

环境变量：
```
gedit ~/.bashrc
```
把下面的内容添加到文件中，保存即可。
```
export MPI_ROOT=/usr/local/mpich2
export PATH=$MPI_ROOT/bin:$PATH
export MANPATH=$MPI_ROOT/man:$MANPATH
```

查看配置情况：
```
mpicc -v
```

#测试案例

```cpp
#include <iostream> 
#include <string.h> 
#include <mpi.h> 
using namespace std;
const int max_string = 200; 
int main () { 
    int comm_sz=0; 
    int my_rank=0; 
    char greeting[max_string]; 
    MPI_Init(NULL,NULL); 
    MPI_Comm_rank(MPI_COMM_WORLD,&my_rank); 
    MPI_Comm_size(MPI_COMM_WORLD,&comm_sz); 
    if(my_rank!=0){ 
        sprintf(greeting,"I am %d",my_rank);
        MPI_Send(greeting,strlen(greeting),MPI_CHAR,0,0,MPI_COMM_WORLD); 
    } 
    else{ 
        cout<<"I am rank "<< my_rank <<endl; 
        for(int i=1;i<comm_sz;i++){ 
            MPI_Recv(greeting,max_string,MPI_CHAR,i,0,MPI_COMM_WORLD,MPI_STATUS_IGNORE); 
            cout<<"receive greeting from "<<i << " is ====> " << greeting <<endl; 
        } 
    } 
    MPI_Finalize (); 
    return 0; 
}
```

编译：

```shell
mpicxx -g -Wall main.cpp -o main.o
mpirun -n 4 ./main.o
```

运行结果：
```shell
I am rank 0
receive greeting from 1 is ====> I am 1
receive greeting from 2 is ====> I am 2
receive greeting from 3 is ====> I am 3
```

# 脱坑

```
configure: error: No Fortran 77 compiler found. If you don't need to
        build any Fortran programs, you can disable Fortran support using
        --disable-fortran. If you do want to build Fortran
        programs, you need to install a Fortran compiler such as gfortran
        or ifort before you can proceed.
```
根据错误可以知道如何解决：
```
./configure -prefix=/usr/local/mpich2 --disable-fortran
```