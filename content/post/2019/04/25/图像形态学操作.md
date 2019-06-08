---
title: "图像的形态学操作"
date: 2019-04-25T11:01:23+08:00
draft: false
tags: ["高性能计算", "OpenMP"]
categories: ["高性能计算", "OpenMP"]
toc: false
---

# 什么是形态学

1. 形态学（形态学）常应用在生物学中，研究动植物的形态和结构;
2. 图像形态学即数学形态学（Mathematical morphology）是一门建立在格伦和拓扑学基础上的图像分析学科，是数学形态学图像处理的基本理论;
3. 常见图像形态学运算：腐蚀，膨胀，开运算，闭运算，骨架抽取，极线腐蚀，击中击不中变换，顶帽变换，颗粒分析，流域变换，形态学梯度等;
4. 最基本的形态学操作是：膨胀（扩张）和腐蚀（侵蚀）;

# 有何作用

1. 消除噪声;
2. 分割出独立的图像元素，在图像中连接相邻的元素;
3. 寻找图像中明显的极大值或极小值区;
4. 求出图像的梯度;

> 【注】：

1. 腐蚀和膨胀是对像素值大的部分而言的，即高亮白部分而不是黑色部分;
2. 膨胀是图像中的高亮部分进行膨胀，领域扩张，效果图拥有比原图更大的高亮区域;
3. 腐蚀是图像中的高亮部分被腐蚀掉，领域缩减，效果图拥有比原图更小的高亮区域;

# 膨胀和腐蚀

## 膨胀

膨胀即为加点操作，将一幅图像A和图像B进行异或的操作，即:C=A⊕B

![201904251101](https://oss.lucoder.com/md/2019/04/25/201904251101.png)

> 途中意思是以正方形`中心`为基准点与被操作图的个个点做操作，得到一个新的图像。

## 腐蚀

腐蚀和膨胀是互相反操作，

# 开闭操作

## 目的

开操作可以平滑物体轮廓，断开狭窄的间断和消除细小的突出物。它具有消除细小物体，在纤细处分离物体和平滑较大物体边界的作用。采用上图的结构B对原件进行开操作。
![20161110160457655](https://oss.lucoder.com/md/2019/04/25/20161110160457655.png)
闭操作可以消弭狭窄的间断，消除小的孔洞。先膨胀后腐蚀的操作称之为闭操作。它具有填充物体内细小空洞，连接邻近物体和平滑边界的作用。采用上图的结构对原件进行闭操作。
![20161110161825931](https://oss.lucoder.com/md/2019/04/25/20161110161825931.png)

## 形态学开运算

开运算（Open Operation）：先腐蚀后膨胀的过程;

### 功能

* 消除小物体;
* 在纤细处分离物体;
* 平滑较大的边界并不明显改变其面积;

## 形态学闭运算

闭运算（Closing Openration），先膨胀后腐蚀;

### 功能

* 排除小型黑洞（黑斑）;


# 实现

```cpp
#include<opencv2/opencv.hpp>
#include<iostream>
using namespace cv;
using namespace std;
Mat src1, src2;
int ment_size=10;
void demo(int,void*);
int main()
{
	src1 = imread("C:\\Users\\马迎伟\\Desktop\\douyin.jpg");
	if (src1.empty())
	{
		cout << "could not find src1" << endl;
		return -1;
	}
	namedWindow("output",CV_WINDOW_AUTOSIZE);
	createTrackbar("elementsize", "output", &ment_size, 21, demo);
	demo(0,0);
	imshow("output",src2);
	waitKey(0);
	return 0;
}
void demo(int ,void*)
{
	int size = 2*ment_size+1;
	//获取 kernel的形状
	Mat ds = getStructuringElement(MORPH_RECT, Size(size, size),Point(-1,-1));
  //dilate(src1, src2, ds);   //膨胀
	erode(src1,src2,ds,Point(-1,-1));      //腐蚀
}
```

```cpp

#include<opencv2/opencv.hpp>
#include<iostream>
using namespace cv;
using namespace std;
Mat src1, src2;
int main()
{
	src1 = imread("C:\\Users\\马迎伟\\Desktop\\douyin.jpg");
	if (src1.empty())
	{
		cout << "could not find src1" << endl;
		return -1;
	}
	namedWindow("output", CV_WINDOW_AUTOSIZE);
	//开 操作，先腐蚀后膨胀；  （深色背景中去 浅色）
	//闭 操作，先膨胀后腐蚀；  （浅色背景中去 深色）
	Mat kernel = getStructuringElement(MORPH_RECT,Size(40,40),Point(-1,-1));
	//cout << kernel << endl;
	//morphologyEx(src1,src2,CV_MOP_OPEN,kernel);
	morphologyEx(src1, src2, CV_MOP_TOPHAT, kernel);
	//imshow("output", src2);
	//形态学梯度，又称为基本梯度，（膨胀减去腐蚀）CV_MOP_GRADIENT
	//黑帽 （闭操作与原图像的差值）  CV_MOP_BLACKHAT
	//顶帽 （原图像与开操作的差值）  CV_MOP_TOPHAT
	imshow("output",src2);
	waitKey(0);
	return 0;
}
```