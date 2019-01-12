---
title: "Visual Studio 2017 配置OpenCV环境"
date: 2018-07-22T14:21:50+08:00
tags: ["机器视觉","图像处理"]
categories: ["机器视觉","图像处理","OpenCV","Visual Studio 环境"]
toc: false
---

# 硬件和环境

|环境  |配置  |
|---------|---------|
|OS     | Windos 10 X64   |
|OpenCV     |     3.2     |
|Visual Studio     | 2017        |

<!--more -->
# 下载OpenCV

OpenCV官方下载地址：[http://opencv.org/releases.html](http://opencv.org/releases.html) 本人选择opencv3.2.0基于Windows平台。读者根据自己需要选择合适版本及平台下载。

![https://oss.lucoder.com/uploads/2018/07/22/20180722161058.png](https://oss.lucoder.com/uploads/2018/07/22/20180722161058.png)

下一步下一步安装即可！

# 环境变量配置

![https://oss.lucoder.com/uploads/2018/07/22/20180722161241.png](https://oss.lucoder.com/uploads/2018/07/22/20180722161241.png)

如图所示，按照配置Java环境的方法，配置两个字段`OpenCV_Home`和`Path`,其中`OpenCV_Home`是配置到`build`层，`Path`里面配置到`bin`，例如：`%OpenCV_Home%\x64\vc14\bin`。然后执行下面命令：

```shell
PS C:\Users\muxuan> opencv_version.exe
3.2.0
PS C:\Users\muxuan>
```
如果可以输出版本信息表示成功！

# Visual Studio 2017 配置

下面在`Visual Studio 2017`中进行配置，如果你还没有安装`Visual Studio 2017`，自己百度。

1. 首先创建一个c++项目：

选择windows 控制台即可。

![https://oss.lucoder.com/uploads/2018/07/22/20180722161440.png](https://oss.lucoder.com/uploads/2018/07/22/20180722161440.png)

2. OpenCV配置

选中`项目`--> `属性`--> `VC++目录`--> `包含目录`--> `添加OpenCV头文件`

![https://oss.lucoder.com/uploads/2018/07/22/20180722162128.png](https://oss.lucoder.com/uploads/2018/07/22/20180722162128.png)


选中`项目`--> `属性`--> `连接器`--> `输入`--> `附加依赖项`--> `附加依赖项`

其中安装的库中有两个文件`opencv_world320d`、`opencv_world320`，开发阶段还是选择`opencv_world320d`.

![https://oss.lucoder.com/uploads/2018/07/22/20180722162426.png](https://oss.lucoder.com/uploads/2018/07/22/20180722162426.png)

# 代码调试

输入下面的测试代码：
```cpp
// MyOpenCV.cpp: 定义控制台应用程序的入口点。
//
#include "stdafx.h" //注意这个放到最前面，有时候可能会报错

#include <highgui.h> //解决cv命名空间找不到问题
#include <opencv2/opencv.hpp>
#include <iostream>

using namespace std;
using namespace cv;

int main()
{
    Mat imgr = imread("C:\\Users\\muxuan\\Desktop\\mylogo.png");
    imshow("奔跑", imgr);
    waitKey(0);

    return 0;
}
```

结果：

![https://oss.lucoder.com/uploads/2018/07/22/20180722163713.png](https://oss.lucoder.com/uploads/2018/07/22/20180722163713.png)

# 错误和提示

1. cv命名空间找不到

如果遇到cv命名空间找不到问题，在头部放上一个引用：

```cpp
#include <highgui.h>
```

1. 调试可能会遇到下面的错误：
```
严重性    代码    说明    项目    文件    行    禁止显示状态
错误    C4996    'fopen': This function or variable may be unsafe. Consider using fopen_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.    MyOpenCV    d:\dev\opencv\3.2\opencv\build\include\opencv2\flann\logger.h    66    
```
然后你可以选择`项目`--> `属性`--> `预处理器`--> `预处理器定义`添加一行数据`_CRT_SECURE_NO_WARNINGS`即可解决问题。

2. 配置时候一定要注意你选择的平台，如果是使用的`x64`活动平台一定要选择`x64`，调试时候也要使用`x64`，否则必然报错：