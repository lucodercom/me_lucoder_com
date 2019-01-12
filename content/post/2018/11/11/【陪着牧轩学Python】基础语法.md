---
title: "【陪着牧轩学Python】（二） 基础篇"
date: 2018-11-11T14:14:22+08:00
categories: ["专题","Python"]
tags: ["专题","Python"]
toc: false
---

# 说明

这一小节内容不多（相对于有基础的同学），包括最基础的变量、变量使用等等。

# 变量

Python对于变量类型非常的敏不敏感，看着是矛盾句子，其实不然，Python不像Java、C#、C++等语法，有严格的类型定义，但是对于类型的使用非常的严格，后期慢慢体会吧，

|类型|案例|说明|
|---|---|---|
|数值|1234|不区分int、float、double|
|字符串|'lucoder'|字符串都懂，不再赘述|
|列表|[1,[2,'three'],4]|em....数组知道吧？不太一样，因为Python显得更加随意|
|字典|{'name':'牧轩','work':'学习Python'}|字典，传说中的键值对|
|元组|(1,'muxuan',2,false)|你是不是觉得元组的内容很熟悉？对就是特殊的列表！|
|文件|reader=open('a.txt','r')|IO资源类型|
|集合|Set('abc'),{'a','b','c'}|这个目前用的不多，还没领会到真正的含义|
|其他|None、bool|其他的类型|

## 写在前面

任何编程都需要了解注释符号和输入输出：

```python
# 是行注释符号

print('Hello world')

```

输入后期给你们说，一步一步来，不要着急。另外，注意Python没有花括号分区域，全靠缩进代替花括号，所以一定要注意缩进问题。

## 数值类型

```python
a=1
b=1
c=a+b
print(c)
c+=1.0
print(c)

pi=3.1415926
r=6
s=pi*math.pow(r,2.0)
print('the s of r is %.10g' % s) # 保留10位（从第一位开始）
print(s)

print('this is a number %.5d',3.1415926)

print('%f' % 1.11)  # 默认保留6位小数
print('%.1f' % 1.11)  # 取1位小数
print('%e' % 1.11)  # 默认6位小数，用科学计数法
print('%.3e' % 1.11)  # 取3位小数，用科学计数法
print('%g' % 1111.1111)  # 默认6位有效数字
print('%.7g' % 1111.1111)  # 取7位有效数字
print('%.2g' % 1111.1111)  # 取2位有效数字，自动转换为科学计数法
```

## 列表类型

列表，简单的当成一个数组吧，先看下面的图，等下有用：

![https://oss.lucoder.com/uploads/2018/11/11/20181112221637.png](https://oss.lucoder.com/uploads/2018/11/11/20181112221637.png)

## 字符串类型

字符串是一个特殊的列表，因为列表的所有元素都是`字符`！现在上面的图有用了，看看下面的代码，先别看结果，猜一下结果。

```python
S='hello I am muxuan'

print(S[0])
print(S[0:6])
print(S[6:])
print(S[-6:])
print(S[6:-6]) #注意后面的空格
print(len(str(S[6:-6])))

#error
#S[0]='z'

S='Hi' + S[5:]
print(S)
```

```python
h
hello
I am muxuan
muxuan
I am
5
Hi I am muxuan
```

稍微点一下，防止真的不懂，`X[start,stop]`，其中X是一个列表，`start`是开始下表，`stop`是结束坐标，`start`和`stop`如果是正直是正向，负数是负向，来给你们一个口诀:
`列表很特殊，方向也要确定。正负两方向，结果却不同。一正向右跑，二正往左行。如遇负数标，逆向定能行`。

## 字典类型

字典类型可以理解为就是键值对组合，这个很简单，不做其他赘述：

```python
dic={'key':'value'}
print(dic.get('key'))
```

## 元组类型

学会了列表，那么元组就很简单了，元组可以理解为一个特殊的元组，他是一个对象（抽象为对象）的最小单位，内容不可修改。用法和列表相同。

```python
tpl=(1,'2',3)
print(tpl[0])
```

## 文件类型

文件：
```txt
me.lucoder.com
me.lucoder.com
me.lucoder.com
```

IO的问题以后会单独讨论，所以看不懂也不用着急。

```python
file=open('e:/t.txt','r+')
readContent=file.readline()
while len(str(readContent)) > 0 :
    print(readContent)
    readContent=file.readline()

file.close()
```

## 其他类型

Python的类型是比较宽松的，会按照类型自己判断，比如：`c=a==b`，c自动就会变成`bool`，所以比较宽松。比较需要注意的问题是`a==None`。

# 语法

```python
d = 225  # 赋值
print(d)
d = 225  # 加
d += 100
print(d)
d = 225  # 减
d -= 100
print(d)
d *= d   #乘
print(d)
d /= 5   #除
print(d)
d=2
d **= 10 #幂运算
print(d)
d=1024
d //= 3 # 整除 向下取整
print(d)
d=1024
d %= 3 # 取余数
print(d)

print(d==a)
print(d!=a)
print(d>a)
print(d<a)
print(d>=a)
print(d<=a)

print( d & a)
print( d | a)
print( d ^ a)
print( ~d)
print( d<<2)
print(d>>5)

print(a and d)
print( a or d)
print(not d)

print(a is int)
print(a is not int)
```

|运算符|描述|
|---|---|
|**	|指数 (最高优先级)
|~ + -	|按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@)
|* / % //	|乘，除，取模和取整除
|+ -	|加法减法
|>> <<	|右移，左移运算符
|&	|位 'AND'
|^ |	位运算符
|<= < > >=	|比较运算符
|<> == !=	|等于运算符
|= %= /= //= -= += *= **=	|赋值运算符
|is is not	|身份运算符
|in not in	|成员运算符
|not and or	|逻辑运算符

# 结束语

到现在你已经掌握Python的基本类型和语法了，为自己鼓掌，噼啦啪啦，噼啦啪啦！！！本章节主要介绍Python 的基本类型和语法。
