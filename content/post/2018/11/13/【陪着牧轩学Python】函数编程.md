---
title: "【陪着牧轩学Python】函数编程"
date: 2018-11-13T14:23:06+08:00
categories: ["专题","Python"]
tags: ["专题","Python"]
toc: false
---

# 说明

经过前面的学习，基础部分学习了一大半了！此处给自己掌声：噼啦啪啦，噼啦啪啦！好，今天我们要结束基础部分，开始最后一节：`函数编程`

```python
def hello(name):
    print("hello {0},welcome to my blog!".format(name))

def add(a,b):
    result = a + b
    print("{0} + {1} = {2}".format(a,b,result))

hello("muxuan")
add(1,2)
```

函数编程，是一个非常常见的模块，一个函数就是一个功能，函数编程很简单，不在继续赘述。但是要看下下面的代码：

```python
import os
print(os.getcwd())
```

哎呀妈呀，懵逼了吧？这是Python的模块机制，em...怎么理解呢，听说过类库么？先这么理解吧。但是具体的到后面面向对象在叙述，但是在此处你要知道这是怎么回事儿。

# 结束语

到现在你已经掌握Python的函数编程了，为自己鼓掌，噼啦啪啦，噼啦啪啦！！！本章节主要介绍Python 的函数编程。
