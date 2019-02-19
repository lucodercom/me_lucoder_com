---
title: "HelloCUDA"
date: 2019-02-11T10:01:23+08:00
draft: false
tags: ["并行计算", "CUDA"]
categories: ["高性能计算", "CUDA"]
toc: false
---

## 引言
本节主要开篇，开始CUDA编程第一步！

## 案例
下面的是Helloworld的案例代码。

```cpp
using namespace std;

__global__ void helloFromGPU()
{
    printf("hello from GPU!\n");
}

int main()
{
    helloFromGPU<<<1,10>>>();
    cudaDeviceReset();
    return 0;
}
```

把上面的代码写到一个文件中，使用下面的编译指令编译即可。
```shell
nvcc -arch sm_60 hello.cu -o hello
./hello
```

效果如下：
```
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
hello from GPU!
```

值得注意的是笔者安装的是CUDA9.2，所以是`sm_60`，这个参数和编译器（CUDA版本）有关，书上的`sm_20`是因为他用的是`Fermi`架构，还有其他的数据如下：
```
3.5 binary code for devices with compute capability 3.5 and 3.7,
5.0 binary code for devices with compute capability 5.0 and 5.2,
6.0 binary code for devices with compute capability 6.0 and 6.1,
PTX code which is compiled to binary code at runtime for devices with compute capability 7.0 and higher.
```

## 总结

书本总结：
```
一个典型的CUDA程序包含个步骤：
1. 分配GPU内存
2. 从CPU内存拷贝数据到GPU内存
3. 调用CUDA函数完成计算
4. 将数据拷贝到CPU内存
5. 释放GPU内存
```

但是上面的代码只有第三部分，因为它太简单了，但是后期的代码会严格按照这五部曲编程。

代码：[https://gitee.com/muxhub/CudaStudy/tree/master/20190219](https://gitee.com/muxhub/CudaStudy/tree/master/20190219)


## 习题

不完全，以后慢慢补充！

|函数|作用|用途|
|---|---|---|
|cudaDeviceReset|cudaDeviceReset重置当前线程所关联过的当前设备的所有资源|如在调用cuda的过程中出现中途错误，需要提前退出程序，可以调用这个cudaDeviceReset来清空之前所关联过得所有资源|
|cudaDeviceSynchronize|会阻塞当前程序的执行，直到所有任务都处理完毕（这里的任务其实就是指的是所有的线程都已经执行完了kernel function）||
|cudaThreadSynchronize|||
|cudaStreamSynchronize|||

nvcc可以编译的文件类型：

|参数|作用|
|||