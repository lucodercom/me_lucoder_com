---
title: "C#调用CUDA的DLL执行GPU计算"
date: 2019-05-16T09:51:51+08:00
draft: false
tags: ["开发", "高性能计算", "GPU"]
categories: ["开发", "高性能计算", "CUDA", ".NET"]
toc: false
---

# C#调用CUDA dll 进行GPU计算

本项目是用C++开发CUDA程序，编译成dll文件供C#调用，实现C#进行GPU计算。

## 创建项目

创建一个空白解决方案：
![20190516095751](https://oss.lucoder.com/md/2019/05/16/20190516095751.png)

创建一个C#项目一个C++项目，注意C++的是动态链接库项目：

![20190516095852](https://oss.lucoder.com/md/2019/05/16/20190516095852.png)

![20190516095938](https://oss.lucoder.com/md/2019/05/16/20190516095938.png)

## 编写C++的CUDA类库

添加一个`cudalib.h`的头文件，输入下面的代码：
```cpp
#define CUDADLL32_API __declspec(dllimport)
extern "C" CUDADLL32_API int vectorAdd(int c[], int a[], int b[], int size);
```

添加CUDA的头文件`kernel.cuh`和CUDA的实现文件`kernel.cuh`。
```cpp
//kernel.cuh
#include "cuda_runtime.h"  
#include "device_launch_parameters.h"    
#include "cudalib.h"
//CUDA核函数  
__global__ void addKernel(int *c, const int *a, const int *b);

//向量相加  
CUDADD_API int vectorAdd(int c[], int a[], int b[], int size)
```
```cpp
//kernel.cu
#include "kernel.cuh"
//CUDA核函数  
__global__ void addKernel(int *c, const int *a, const int *b)
{
	int i = threadIdx.x;
	c[i] = a[i] + b[i];
}


//向量相加  
CUDADLL32_API int vectorAdd(int c[], int a[], int b[], int size)
{
	int result = -1;
	int *dev_a = 0;
	int *dev_b = 0;
	int *dev_c = 0;
	cudaError_t cudaStatus;
	// 选择用于运行的GPU  
	cudaStatus = cudaSetDevice(0);
	if (cudaStatus != cudaSuccess) {
		result = 1;
		goto Error;
	}
	// 在GPU中为变量dev_a、dev_b、dev_c分配内存空间.  
	cudaStatus = cudaMalloc((void**)&dev_c, size * sizeof(int));
	if (cudaStatus != cudaSuccess) {
		result = 2;
		goto Error;
	}
	cudaStatus = cudaMalloc((void**)&dev_a, size * sizeof(int));
	if (cudaStatus != cudaSuccess) {
		result = 3;
		goto Error;
	}
	cudaStatus = cudaMalloc((void**)&dev_b, size * sizeof(int));
	if (cudaStatus != cudaSuccess) {
		result = 4;
		goto Error;
	}
	// 从主机内存复制数据到GPU内存中.  
	cudaStatus = cudaMemcpy(dev_a, a, size * sizeof(int), cudaMemcpyHostToDevice);
	if (cudaStatus != cudaSuccess) {
		result = 5;
		goto Error;
	}
	cudaStatus = cudaMemcpy(dev_b, b, size * sizeof(int), cudaMemcpyHostToDevice);
	if (cudaStatus != cudaSuccess) {
		result = 6;
		goto Error;
	}
	// 启动GPU内核函数  
	addKernel << <1, size >> > (dev_c, dev_a, dev_b);

	// 采用cudaDeviceSynchronize等待GPU内核函数执行完成并且返回遇到的任何错误信息  
	cudaStatus = cudaDeviceSynchronize();
	if (cudaStatus != cudaSuccess) {
		result = 7
			goto Error
	}
	// 从GPU内存中复制数据到主机内存中  
	cudaStatus = cudaMemcpy(c, dev_c, size * sizeof(int), cudaMemcpyDeviceToHost);
	if (cudaStatus != cudaSuccess) {
		result = 8;
		goto Error;
	}
	result = 0;
	// 重置CUDA设备，在退出之前必须调用cudaDeviceReset  
	cudaStatus = cudaDeviceReset();
	if (cudaStatus != cudaSuccess) {
		return 9;
	}
Error:
	//释放设备中变量所占内存  
	cudaFree(dev_c);
	cudaFree(dev_a);
	cudaFree(dev_b);
	return result;
}
```

## 代码编译

默认是MSVC编译的，可能不识别CUDA的符号，所以需要修改为CUDA的NVCC编译符号：

![20190516101353](https://oss.lucoder.com/md/2019/05/16/20190516101353.png)

![20190516101411](https://oss.lucoder.com/md/2019/05/16/20190516101411.png)

修改cu文件属性：

![20190516103048](https://oss.lucoder.com/md/2019/05/16/20190516103048.png)

![20190516103103](https://oss.lucoder.com/md/2019/05/16/20190516103103.png)

输入库`cudart.lib`：

![20190516103252](https://oss.lucoder.com/md/2019/05/16/20190516103252.png)

重新生成项目：

![20190516101711](https://oss.lucoder.com/md/2019/05/16/20190516101711.png)

## C#调用

```cs
class CudaRunner
    {
        public void Run()
        {
            var a = new int[] { 1, 2, 3, 45, 456, 454, 1, 4, 65, 32, 456, 1, 56, 32, 512, 3, 5416, 86, 54, 4236, 12, 113, 321 };
            var b = new int[] { 1, 2, 3, 45, 456, 454, 1, 4, 65, 32, 456, 1, 56, 32, 512, 3, 5416, 86, 54, 4236, 12, 113, 321 };
            var c = new int[a.Length];

            vectorAdd(c, a, b, a.Length);

            for (int i = 0; i < c.Length; i++)
            {
                Console.WriteLine("{0} + {1} = {2}", a[0], b[i], c[i]);
            }
        }

        [DllImport("../../../Debug/CudaLib.dll", CallingConvention = CallingConvention.StdCall)]
        public static extern int vectorAdd(int[] c, int[] a, int[] b, int size);
    }
```

```cs
var run = new CudaRunner();
run.Run();
Console.Read();
```

Code:[]()

# 结果

![20190516105035](https://oss.lucoder.com/md/2019/05/16/20190516105035.png)

# 遇到问题

![20190516104944](https://oss.lucoder.com/md/2019/05/16/20190516104944.png)

切换成为对应的平台：
![20190516105002](https://oss.lucoder.com/md/2019/05/16/20190516105002.png)
