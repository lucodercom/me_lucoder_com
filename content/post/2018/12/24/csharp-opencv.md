---
title: "使用C#开发Opencv项目"
date: 2018-12-24T10:01:23+08:00
draft: false
tags: ["机器视觉","Opencv","图像处理"]
categories: ["机器视觉","Opencv"]

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
# comment: false
toc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
# contentCopyright: false
# reward: false
# mathjax: false
---

如果仅仅是简单处理图像，建议使用C++或者Python版本的Opencv，但是很明显无论是C++还是Python都对UI显示有不同层次的支持不足，但是C#天生的编写UI优势。C# 使用Opencv有两种方式，一般的是OpencvSharp和Egu.CV，但是Egu操作很复杂，所以选择了OpencvSharp，但是OpencvSharp是调用的原生的C++版本的Opencv。

![两种Opencv](https://oss.lucoder.com/uploads/2018/12/24/20181224100123.png)
# 创建项目

创建一个普通的C#项目即可，不在赘述，我创建的是一个WPF项目。

# 安装组建

首先打开Nuget，输入Opencv。你可以看到下面的界面。

![两种Opencv](https://oss.lucoder.com/uploads/2018/12/24/20181224100123.png)


```cs
OpenFileDialog ofd = new OpenFileDialog();
ofd.Title = "选择图片";

if (ofd.ShowDialog() ?? false)
{
    Mat mat = new Mat(ofd.FileName, ImreadModes.AnyDepth);
    Cv2.ImShow("txt", mat);
    Cv2.WaitKey(0);
}
```

# 运行项目

你将会看到熟悉的界面。