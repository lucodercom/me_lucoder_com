---
title: "【陪着牧轩学Python】分支循环"
date: 2018-11-13T14:21:50+08:00
categories: ["专题","Python"]
tags: ["专题","Python"]
toc: false
---

# 说明

咳咳，昨天的东西忘得差不多了吧？没事儿，文章都在这！然后来一个今天的小战利品：
```python
skip_row = 3
skip_flag = -1
file = open('E:/read.txt','r+')
read = "default"
list=[]
while len(str(read)) > 0:
    skip_flag += 1
    read = file.readline()
    if skip_flag % skip_row ==  0 and len(read) > 0:
        list.insert(skip_flag,round(float(read.strip()),2))
    else:
        continue
file.close()
print(list)
print(len(list))
```
我只说一下功能：按行读取文件中数据，把每一行转换为浮点数保留两位小数，这是我的一个需求，不过对于你们来说估计没什么用，用到了昨天学的数据类型，以及今天的分支循环。

# 分支语句

分支语句是编程逻辑的一个非常`重要的一块儿`！

![https://oss.lucoder.com/uploads/2018/11/13/20181113210233.png](https://oss.lucoder.com/uploads/2018/11/13/20181113210233.png)

分支语句就是将自然语言的`如果...那么...`，或者是`如果...或者...否则...`转换为`if ... else ...`即可，python中典型的是：
```python
if [bool]:
    do work...
[
elif [bool]:
    do work...
]
[else:]
```

案例：
```python
score=10
if score / 10 > 8 :
    print('优秀')
elif score / 10 > 7 :
    print('良好')
elif score / 10 > 6:
    print('及格')
elif score / 10 > 4:
    print('不及格')
else:
    print('孩子回家吧')
```

这个判断好多人都见过，就是输入一个分数，判断成绩类型的一个判断。

或许有人说还有`switch`语句呀，错！Python没有`switch`！！！

# 循环语句

啥是循环语句？举个例子：1+2+3+...+100等于几?先别管计算机是怎么计算的，先想想自己是怎么算的，em...不要耍小聪明，说什么1+100然后2+99...，因为计算机智商不足3岁孩童，所以你不要耍小聪明！那咋办，小孩子不都是掰着手指头一个一个算的么？有没有发现工作都是重复性的工作一直重复的计算加法，对，我们转换到计算机怎么干呢？`重复性工作交给循环`。

![https://oss.lucoder.com/uploads/2018/11/13/20181113213108.png](https://oss.lucoder.com/uploads/2018/11/13/20181113213108.png)

## for 循环

for循环是python中常用的一个循环：

![https://oss.lucoder.com/uploads/2018/11/13/20181113213410.png](https://oss.lucoder.com/uploads/2018/11/13/20181113213410.png)

Python中的For循环结构如下：
```
for iterating_var in sequence:
   statements(s)
```

好，现在继续分析题目，1+2+3+...+100，是否需要一个变量保存计算的结果呢？首先灌输一个需要记忆的内容`range(m,n)`生成一个`m-(n - 1)`的迭代，我靠！什么是迭代？好吧，没有基础的理解迭代或许有困难，你可以理解为一个顺序取数的一个“人”，它能够从`m-n`中，从`m`开始，取到`n - 1`，比如：`range(1,101)`，取得就是：`1,2,3,4,5...,99,100`，能理解了么?好吧，继续工作吧，先别管他了。

```python
sum=0
for i in range(1,101):
    sum += i
print(sum)
```

怎么样，简单吧，这个就像是一只手`i`从一堆数据`range(1,100)`中，按照从`1-100`的顺序，取出来数，放到i中，然后`sum=sum+i`算出最终结果。

其实用处更多是下面的方法：
```python
for i in 'me.lucoder.com':
    print(i)

list=['1',2,'a']
for i in list:
    print(i)
```

## while循环

while循环也是一个非常重要的循环，尤其是IO的操作以及后期的数据库的操作。

![https://oss.lucoder.com/uploads/20181113/20181113215136.png](https://oss.lucoder.com/uploads/20181113/20181113215136.png)

while循环咋跑的，看下面的这个图：

![http://www.runoob.com/wp-content/uploads/2013/11/loop-over-python-list-animation.gif](http://www.runoob.com/wp-content/uploads/2013/11/loop-over-python-list-animation.gif)

> 图片来自[菜鸟](http://www.runoob.com/python/python-while-loop.html)，大家也可以去这里学一下Python，有些地方讲的非常清楚。

在此再次分析上面的案例：

```python
sum = 0
i = 0
while i < 100:
    i += 1
    sum += i
print(sum)
```

其他用途：

非常重要的一个用途就是开题的小案例。里面有一块儿读取文件内容的。还有就是数据库操作的，以后我们再说。

## 相互转换

其实循环之间是可以相互转换的，比如本文的`1-100`数和的案例。

# 最后唠叨

其实分支循环到此都已经结束了，但是还有一小点儿内容，不想单独开设一章了，所以在此说明吧。

你有没有想过一个问题？比如我想寻找`me.lucoder.com`位置，比如下面的案例：
```python
file = open('file.txt','r+')
read = ""
flag = False
pattern = "me.lucoder.com"
rowIndex = 0
while True:
    read = file.readline()
    rowIndex += 1
    if(len(read) <= 0):
        flag = True
        break
    
    if read.find(pattern) < 0:
        pass
    else:
        print(rowIndex)
        continue
    print('hello')

file.close()
```
> 注意：此处是为了表示出来三个不同的关键字而写的代码，真是业务中毫无意义！

|关键字|说明|
|---|---|
|pass|特殊关键字，只是为了保持结构完整，啥也不工作|
|continue|特殊关键字，跳过当前循环，开始下一次循环|
|break|跳出循环，结束循环体|

`break`和`continue`的区别就是作用的区域，如果是结束一次循环就用`continue`，如果是结束循环体就用`break`。比如上述循环，如果到第`11`行成功，那么直接就会跳转到`20`行，继续顺序执行。当运行到`15`行，则会跳转到`6`行继续工作。

# 结束语

到现在你已经掌握Python的分支循环了，为自己鼓掌，噼啦啪啦，噼啦啪啦！！！本章节主要介绍Python 的分支循环。
