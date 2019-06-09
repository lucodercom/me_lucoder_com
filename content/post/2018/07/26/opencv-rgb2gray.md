---
title: "OpenCV将RGB转换为单通道灰图"
date: 2018-07-26T14:21:50+08:00
tags: ["机器视觉","图像处理"]
categories: ["机器视觉","图像处理","OpenCV","通道转换"]
toc: false
---

# 环境

|项目 |参数|
|---|---|
|OS |Windows 10 Professional amd x64|
|IDE | Visual Studio 2017 Professional|
|OpenCV |3.2 |

<!-- more-->
# 思路

* 首先使用`cvLoadImage`函数将图片读入到一个I`plImage`中。

```cpp
/* load image from file
  iscolor can be a combination of above flags where CV_LOAD_IMAGE_UNCHANGED
  overrides the other flags
  using CV_LOAD_IMAGE_ANYCOLOR alone is equivalent to CV_LOAD_IMAGE_UNCHANGED
  unless CV_LOAD_IMAGE_ANYDEPTH is specified images are converted to 8bit
*/
CVAPI(IplImage*) cvLoadImage( const char* filename, int iscolor CV_DEFAULT(CV_LOAD_IMAGE_COLOR));
```

* 使用`cvCreateImage`创建单通道图片容器，用来存放图片。

```cpp
/** @brief Creates an image header and allocates the image data.

This function call is equivalent to the following code:
@code
    header = cvCreateImageHeader(size, depth, channels);
    cvCreateData(header);
@endcode
@param size Image width and height
@param depth Bit depth of image elements. See IplImage for valid depths.
@param channels Number of channels per pixel. See IplImage for details. This function only creates
images with interleaved channels.
 */
CVAPI(IplImage*)  cvCreateImage( CvSize size, int depth, int channels );
```

* 使用`cvSetZero`用来清除图片中的数据。

```cpp
/** @brief Clears the array.

The function clears the array. In the case of dense arrays (CvMat, CvMatND or IplImage),
cvZero(array) is equivalent to cvSet(array,cvScalarAll(0),0). In the case of sparse arrays all the
elements are removed.
@param arr Array to be cleared
 */
CVAPI(void)  cvSetZero( CvArr* arr );
```

* 使用`cvMerge`用来合并图片中的数据。

```cpp
/** Merges a set of single-channel arrays into the single multi-channel array
   or inserts one particular [color] plane to the array */
CVAPI(void)  cvMerge( const CvArr* src0, const CvArr* src1,
                      const CvArr* src2, const CvArr* src3,
                      CvArr* dst );
```

* 使用`cvShowImage`用来显示处理结果。

```cpp
/* display image within window (highgui windows remember their content) */
CVAPI(void) cvShowImage( const char* name, const CvArr* image );
```

* 使用`cvCvtColor`转换为灰图

```cpp
/** @brief Converts input array pixels from one color space to another
@see cv::cvtColor
*/
CVAPI(void)  cvCvtColor( const CvArr* src, CvArr* dst, int code );
```

# 将RGB转换为单通道

## 代码

下面部署真实代码：
```cpp
#include "stdafx.h" //注意这个放到最前面，有时候可能会报错

#include <highgui.h> //解决cv命名空间找不到问题
#include <opencv2/opencv.hpp>
#include <iostream>

using namespace std;
using namespace cv;

int main()
{
	IplImage* imgload = cvLoadImage("20180726095642.png", 1);
	IplImage* Bimg = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 1);
	IplImage* Gimg = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 1);
	IplImage* Rimg = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 1);
	
	cvSplit(imgload, Bimg, Gimg, Rimg, 0);
	
	IplImage* pImg1 = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 3);
	IplImage* pImg2 = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 3);
	IplImage* pImg3 = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 3);

	cvSetZero(pImg1);
	cvSetZero(pImg2);
	cvSetZero(pImg3);

	cvMerge(Bimg, 0, 0, 0, pImg3);
	cvMerge(0, Gimg, 0, 0, pImg2);
	cvMerge(0, 0, Rimg, 0, pImg1);

	cvShowImage("RGB", imgload);
	cvShowImage("R", pImg1);
	cvShowImage("G", pImg2);
	cvShowImage("B", pImg3);

	waitKey(0);

	return 0;
}
```

## 处理结果

* 原始图

![https://oss.lucoder.com/uploads/2018/07/26/20180726095642.png](https://oss.lucoder.com/uploads/2018/07/26/20180726095642.png)

* R图

![https://oss.lucoder.com/uploads/2018/07/26/20180726095536.png](https://oss.lucoder.com/uploads/2018/07/26/20180726095536.png)

* G图

![https://oss.lucoder.com/uploads/2018/07/26/20180726095614.png](https://oss.lucoder.com/uploads/2018/07/26/20180726095614.png)

* B图

![https://oss.lucoder.com/uploads/2018/07/26/20180726095555.png](https://oss.lucoder.com/uploads/2018/07/26/20180726095555.png)

# 对图片进行灰度处理

```cpp
IplImage* ImgGray = cvCreateImage(cvGetSize(imgload), IPL_DEPTH_8U, 1);
cvCvtColor(pImg1, ImgGray, CV_BGR2GRAY);
```

## 结果

* R图

![https://oss.lucoder.com/uploads/2018/07/26/20180726140911.png](https://oss.lucoder.com/uploads/2018/07/26/20180726140911.png)

* G图

![https://oss.lucoder.com/uploads/2018/07/26/20180726140937.png](https://oss.lucoder.com/uploads/2018/07/26/20180726140937.png)

* B图

![https://oss.lucoder.com/uploads/2018/07/26/20180726140959.png](https://oss.lucoder.com/uploads/2018/07/26/20180726140959.png)

# 总结

本文叙述了如何使用OpenCV将RGB图像转换为单通道的图像，然后对单通道图像进行灰度化，主要用到的函数有`cvLoadImage`、`cvCreateImage`、`cvSetZero`、`cvMerge`、`cvShowImage`、`cvCvtColor`等。