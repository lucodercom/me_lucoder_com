---
title: "使用C#的Parallel类进行并行计算"
date: 2019-06-11T18:01:23+08:00
draft: false
tags: ["机器视觉", "Opencv", "图像处理"]
categories: ["机器视觉", "Opencv"]
toc: false
---

 `并行计算是一门艺术`，是不是有点艺术化了，但是事实却是如此，使用C++做并行程序设计天生有OpenMP支持，另外使用三方的有`MPI`，但是对于C#而言只能通过`P/I`的方式使用MPI，性能大打折扣。最近研究发现，原来在C#内部天生有一个和`OpenMP`相似的类`Parallel`，该类可以很方便的完成并行计算程序。

 # Parallel类API

```cs
public static ParallelLoopResult For<TLocal>(int fromInclusive, int toExclusive, Func<TLocal> localInit, Func<int, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult For(int fromInclusive, int toExclusive, Action<int> body);

public static ParallelLoopResult For(long fromInclusive, long toExclusive, Action<long> body);

public static ParallelLoopResult For(int fromInclusive, int toExclusive, ParallelOptions parallelOptions, Action<int> body);

public static ParallelLoopResult For(long fromInclusive, long toExclusive, ParallelOptions parallelOptions, Action<long> body);

public static ParallelLoopResult For(int fromInclusive, int toExclusive, Action<int, ParallelLoopState> body);

public static ParallelLoopResult For(long fromInclusive, long toExclusive, Action<long, ParallelLoopState> body);

public static ParallelLoopResult For(int fromInclusive, int toExclusive, ParallelOptions parallelOptions, Action<int, ParallelLoopState> body);

public static ParallelLoopResult For(long fromInclusive, long toExclusive, ParallelOptions parallelOptions, Action<long, ParallelLoopState> body);

public static ParallelLoopResult For<TLocal>(long fromInclusive, long toExclusive, Func<TLocal> localInit, Func<long, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult For<TLocal>(int fromInclusive, int toExclusive, ParallelOptions parallelOptions, Func<TLocal> localInit, Func<int, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult For<TLocal>(long fromInclusive, long toExclusive, ParallelOptions parallelOptions, Func<TLocal> localInit, Func<long, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource>(Partitioner<TSource> source, Action<TSource, ParallelLoopState> body);

public static ParallelLoopResult ForEach<TSource>(OrderablePartitioner<TSource> source, Action<TSource, ParallelLoopState, long> body);

public static ParallelLoopResult ForEach<TSource, TLocal>(Partitioner<TSource> source, Func<TLocal> localInit, Func<TSource, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource, TLocal>(OrderablePartitioner<TSource> source, Func<TLocal> localInit, Func<TSource, ParallelLoopState, long, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, Action<TSource, ParallelLoopState> body);

public static ParallelLoopResult ForEach<TSource>(Partitioner<TSource> source, ParallelOptions parallelOptions, Action<TSource, ParallelLoopState> body);

public static ParallelLoopResult ForEach<TSource>(Partitioner<TSource> source, Action<TSource> body);

public static ParallelLoopResult ForEach<TSource>(OrderablePartitioner<TSource> source, ParallelOptions parallelOptions, Action<TSource, ParallelLoopState, long> body);

public static ParallelLoopResult ForEach<TSource>(Partitioner<TSource> source, ParallelOptions parallelOptions, Action<TSource> body);

public static ParallelLoopResult ForEach<TSource, TLocal>(IEnumerable<TSource> source, ParallelOptions parallelOptions, Func<TLocal> localInit, Func<TSource, ParallelLoopState, long, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource, TLocal>(OrderablePartitioner<TSource> source, ParallelOptions parallelOptions, Func<TLocal> localInit, Func<TSource, ParallelLoopState, long, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource, TLocal>(IEnumerable<TSource> source, ParallelOptions parallelOptions, Func<TLocal> localInit, Func<TSource, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource, TLocal>(IEnumerable<TSource> source, Func<TLocal> localInit, Func<TSource, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, ParallelOptions parallelOptions, Action<TSource, ParallelLoopState, long> body);

public static ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, Action<TSource, ParallelLoopState, long> body);

public static ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, ParallelOptions parallelOptions, Action<TSource, ParallelLoopState> body);

public static ParallelLoopResult ForEach<TSource, TLocal>(Partitioner<TSource> source, ParallelOptions parallelOptions, Func<TLocal> localInit, Func<TSource, ParallelLoopState, TLocal, TLocal> body, Action<TLocal> localFinally);

public static ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, ParallelOptions parallelOptions, Action<TSource> body);

public static ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, Action<TSource> body);
       
public static ParallelLoopResult ForEach<TSource, TLocal>(IEnumerable<TSource> source, Func<TLocal> localInit, Func<TSource, ParallelLoopState, long, TLocal, TLocal> body, Action<TLocal> localFinally);

public static void Invoke(ParallelOptions parallelOptions, params Action[] actions);

public static void Invoke(params Action[] actions);
```

上面列举了所有的API，但是平时或许用不到这么多API，最常用的还是`For`循环的并行遍历方式：
```cs
Parallel.For(startIndex, stopIndex, new Action((index)=>
    { 
        //work for index! 
    }
));
```
 # 像素处理案例

下面是一个`SharpOpenCV`遍历像素点的并行案例，使用target二值化掩膜中的数据标记原始图像的方法，根据target中非0的点用绿色标记到src中：
```cs
private Mat Mark(Mat src, Mat target)
{
    var iResult = src.Clone();
    int height = src.Rows < target.Rows ? src.Rows : target.Rows;
    int width = src.Cols < target.Cols ? src.Cols : target.Cols;
    iResult = src.Clone();
    
    var targetIter = target.GetGenericIndexer<Vec3b>();
    var resultIter = iResult.GetGenericIndexer<Vec3b>();
    
    Parallel.For(0, height, (i) =>
        {
            for (int j = 0; j < width; j++)
            {
                if (targetIter[i, j].Item0 > 100)
                iResult.Set(i, j, new Vec3b(0, 255, 0));
            }
        });
    return iResult;
}
```

# 时间性能

加速比公式：

$$Speedup = \frac {S}{P}$$

|Way|Time|Speedup|E|
|---|---|---|---|
|String||1||
|Parallel||||
|Parallels||||

# 总结

从上面的实验可以看出，加速效果还是非常明显的，
