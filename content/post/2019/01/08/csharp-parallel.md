---
title: "C#并行算法"
date: 2019-01-08T21:01:23+08:00
draft: false
tags: ["Parallel", "高性能计算", "C#","并行计算"]
categories: ["高性能计算", "C#","Parallel"]
toc: false
---

用了这么多次的C#，总在抱怨C#效率低，其实不然，C#可能是启动效率低，一旦运行起来或许性能比C++差不了多少（前提GC关掉），尤其是`Span<T>`的出现，所以，今天来唠嗑一下如何提高C#运算性能。

# 并行程序设计

并行程序设计在本节不会过多设计，因为本篇主要讲如何使用C#进行高性能计算，今天主要讲的是如何使用C#中的`Parallel`进行并行计算。

> PS : Lucoder 类库支持本机、多机并行计算。

# Parallel类

```
Parallel.For(int start,int stop,Action<int> delgate);

Parallel.For(int start,int stop,Action<int> delgate);

Parallel.For(int start,int stop,Action<int> delgate);

Parallel.For(int start,int stop,Action<int> delgate);
```
