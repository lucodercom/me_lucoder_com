---
title: "OpenMP嵌套循环"
date: 2019-04-08T10:01:23+08:00
draft: false
tags: ["高性能计算", "OpenMP"]
categories: ["高性能计算", "OpenMP"]
toc: false
---

# 说明

有时候开发过程中或许会遇到多重循环的现象，如果只是在最外层放上一个OpenMP的并行计算标志，那么只有在第一层for循环才会并行计算，循环内部无论多少循环都会串行的方式执行。

# 解决办法

使用`omp_set_nested()`方法和标记结合的方式可以实现嵌套循环并行计算。其中参数可以是0或者1,0表示关闭嵌套循环并行计算，如果是1表示开启嵌套循环并行计算。然后在内部循环上面添加OpenMP的并行计算标志就可以实现嵌套内部的循环可以并行计算。

# 使用方法

```cpp
//File :NestedTest.cpp
#include"stdafx.h"
#include<omp.h>
#include<iostream>
using namespace std;
void NestedTest()
{
	int i,j,k;
	k=omp_get_nested();
	cout<<"是否支持并行嵌套："<<k<<"\n";
	omp_set_nested(1);//设置支持嵌套并行
	k=omp_get_nested();
	cout<<"是否支持并行嵌套："<<k<<"\n";
	double starttime,endtime;
	starttime=omp_get_wtime();
	#pragma omp parallel for
	for(i=0;i<2;i++)
	{
		#pragma omp parallel for
		for(j=0;j<4;j++)
		{
			cout<<"  i="<<i<<"  j="<<j<<"  Threads:"<<omp_get_num_threads()<<"  ThreadID:"<<omp_get_thread_num()<<"\n";
		}
		cout<<"  I="<<i<<"  Threads:"<<omp_get_num_threads()<<"  ThreadID:"<<omp_get_thread_num()<<"\n";
	}	
	endtime=omp_get_wtime();
	k=omp_get_nested();
	cout<<"是否支持并行嵌套："<<k<<"\n";
	cout<<"计算耗时："<<endtime-starttime<<"s\n";
}
```

# 效果

代码可以赋值到自己的项目中测试一下，可以发现内层循环和外层循环都是并行的计算。

# 坑

虽然可以实现内部外部都并行计算，但是效率并不一定会比只在外层放并行标志，因为并行计算不一定可以提高计算效率，需要在合适的地方才会起到事半功倍的效果，否则只可能是事倍功半！