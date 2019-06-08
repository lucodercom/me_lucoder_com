---
title: "C#开发MPI应用"
date: 2019-05-22T20:24:25+08:00
draft: false
tags: ["开发", "高性能计算", "MPI"]
categories: ["开发", "高性能计算", "MPI", "MPI.NET"]
toc: false
---

# MPI.NET

作者的Github:[https://github.com/jmp75/MPI.NET](https://github.com/jmp75/MPI.NET)

文本代码:等待上传

# 创建项目

> 注意Framework版本不要低于 4.6.2！

![20190522202425](https://oss.lucoder.com/md/2019/05/22/20190522202425.png)

# 安装类库

使用`Nuget`安装类库：

![20190522202450](https://oss.lucoder.com/md/2019/05/22/20190522202450.png)

![20190522202559](https://oss.lucoder.com/md/2019/05/22/20190522202559.png)

# 使用MPI

```cs
public void RunMpiAction(string[] args)
{
    using (new MPI.Environment(ref args))
    {
        //切割数据
        int rank = Communicator.world.Rank - 1;
        int ranks = Communicator.world.Size;
        rank += 1;
        if (rank == 0){
            //数据分为groups组
            int groups = 5;
            for (int i = 1; i < ranks; i++)
            {
                Communicator.world.Send(groups, i, i);
            }
        }
        
        if (rank != 0)
        {
            int groups = Communicator.world.Receive<int>(0, rank);
            Console.WriteLine("rank {0} , groups {1}", rank, groups);
            var list = new List<string>() { $"{rank} - a", $"{rank} - b", $"{rank} - c" };
            //rank -= 1;
            Communicator.world.Send(list, 0, rank);
            Console.WriteLine("current rank is {0}", rank);
        }
        
        if (rank == 0){
            var result = new List<string>();
            for (int i = 1; i < ranks; i++)
            {
                var list = Communicator.world.Receive<List<string>>(i, i);
                result.AddRange(list);
            }
            
            //接受结果
            Console.WriteLine("current rank is {0}", rank);
            for (int i = 0; i < result.Count; i++){
                Console.WriteLine(result[i]);
            }
        }
    }
}
```
# 运行结果

> 需要安装`msmpi`，不要点击调试运行，需要使用命令执行。

![20190522203302](https://oss.lucoder.com/md/2019/05/22/20190522203302.png)