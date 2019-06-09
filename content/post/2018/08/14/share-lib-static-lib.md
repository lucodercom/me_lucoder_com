---
title: "动态链接库和静态链接库"
date: 2018-08-14T14:21:50+08:00
tags: ["Ubuntu","链接库"]
categories: ["Ubuntu","链接库"]
toc: false
---

知识不等于技术，这句话真的是越工作的时间长越深有体会，学习到的知识只有不断的实践，才成真正在自已的心里扎下根，成为自身的一部分，所以无论如何，我希望我的博客可以一直写下去，慢慢的沉淀，终会有回报，无聊的时候想想，的确，写代码也是一种自娱自乐！

前几天在做项目时出了一个问题，大体就是：makefile里在编译可执行文件testappd的时候在有用-l去引用一个libtest.so，能编译通过，但是在加载运行的时候系统提示找不到这个lib而导致进程启动失败。后来知道在产品版本编译打包的时候这个lib并未被打包进去，这个问题后来解决了。但是出于好奇，我想搞清楚动态库和静态库的概念，以及之前做过另一个项目时以dlopen方式去引用动态库和makefile 去引用动态库这几种方式的不同。查找了网上的一些资料，看明白了，还需手动验证一下才放心。

首先说说静态链接库，说实话，有时候真心感觉计算机这些相关的各种高大上的词汇都是纸老虎，看上去高深的不行，其实当你捅开那层纸，就那么回事儿。

所谓静态链接库，说白了就是在你把写好的代码编译的时候，就把你引用的库一起给编进去了，从此后你编出来的执行程序跟外面都不再有任何关系，即使这个库更新了，你也搭不上边儿，其次，如果系统中许多类似的程序都需要用到这个库，那么各自在编译的时候都需要把这个库给编进去，浪费存储空间（加载到内存里应该也是浪费内存空间的）。linux系统中静态库的名字一般叫

xxx.a, 所以如果你看到一个以 .a结束的文件那么它多半就是一个静态链接库文件。

    废话少说，我们直接上干货，看看静态库是如何被编译出来以及如何被使用的

    首先我们写了一个sum.c，如下，很简单，里面只有一个Sum函数，把两个数相加的和返回

```cpp
int Sum(int Number1, int Number2)
{
    return Number1 + Number2;
}
```
当然还要写一个声明它的sum.h

```cpp
int Sum(int Number1, int Number2);
```
最后来一个调用这个sum函数的主函数, 打印返回的结果
```
#include<stdio.h>
#include<sum.h>

void main()
{
    int Num1 = 1;
    int Num2 = 2;
    int iRet = 0;

    iRet = Sum(Num1, Num2);
    printf("Num1 + Num2 = %d.", iRet);
    return;
}
```
接下来我们编译静态库，我的笔记本是mac os环境，打开终端后用vim写代码， gcc等相关工具直接就能用，版本没注意，反正能用就行
```shell
192:zch kane$ ls
main.c    sum.c    sum.h
192:zch kane$ gcc -c sum.c
192:zch kane$ ls
main.c    sum.c    sum.h    sum.o
192:zch kane$ 
192:zch kane$ 
192:zch kane$ ar cr libsum.a sum.o
192:zch kane$ ls
libsum.a    main.c        sum.c        sum.h        sum.o
192:zch kane$
```
如上，我们用ar这个工具来编译静态库，cr标志告诉ar将object文件封装

然后我们编译主程序并运行：

```shell
192:zch kane$ gcc -o sumappd main.c -L . -lsum
192:zch kane$ ls
libsum.a    main.o        sum.h        sumappd
main.c        sum.c        sum.o
192:zch kane$ ./sumappd 
Num1 + Num2 = 3
```

“-L .”指明了当前lib所在的路径是在本目录，-l表示链接libsum.a这个lib库，很好理解。

 所谓动态链接库，也就是说编译的时候不会真的把你引用到的库给编到你的执行程序里，而是在执行时候才会去加载相关的库，所有用到此库的程序可以共享一份代码，这样带来的好处是可执行程序所占的空间变小了，同时，如果库需要升级，你并不需要重新编译你的程序，只要把相关的库升级即可。

   接下来我们来看看动态链接库的编译与使用方法，代码还是和上文中的一样，分别为​sum.c, sum.h, 和main.c, 在linux下动态链接库文件一般叫做libxxx.so。

```shell
192:zch kane$ ls
main.c    sum.c    sum.h
192:zch kane$ 
192:zch kane$ gcc -c -fPIC sum.c                            ------  -fIPC告诉编译器将源代码编译成共享的object文件,PIC(Position-Independent Code)意思是非位置依赖性代码
192:zch kane$ ls
main.c    sum.c    sum.h    sum.o
192:zch kane$ gcc -shared -fPIC -o libsum.so sum.o           --------  生成动态链接库文件libsum.so
192:zch kane$ ls
libsum.so    main.c        sum.c        sum.h        sum.o
192:zch kane$ 
192:zch kane$ gcc -o sumappd main.c -L . -lsum               --------- 生成可执行程序
192:zch kane$ ls
libsum.so    sum.c        sum.o
main.c        sum.h        sumappd
192:zch kane$ ./sumappd                                      ---------运行生成结果
Num1 + Num2 = 3
```

 ps:所以大家看到了，编译时”-lsum“的方式，是不能够区分当前是静态链接还是动态链接的。如果在同一个目录下同时有静态链接库和动态链接库，则系统默认会引用动态链接库，如果想使用静态链接库则需要在编译时加上”-static“参数（具体方法可自行百度）。

    共享库，有两种形式，第一种就是在上一篇文章中说到的“动态链接库”，而共享库的另一种形式，则被称之为“动态加载库”，也就是我刚才提到的用“dlopen”方式来玩的。动态加载库在编译的时候，应该是不需要去-l引用lib，而是在可执行程序中，可以自已决定加载库的时机。比如程序跑着跑着，突然想用libabc.so库里的一个叫abc的函数了，这时就可以用dlopen去打开这个库，然后使用dlsym去找到abc的函数指针并调用即可。

   这几个以“dl”开头的函数，叫动态加载API，提供了以下这么几个接口来支持动态加载操作：
```cpp
void *dlopen( const char *file, int mode ); 
```
    dlopen的函数功能是打开库文件并返回文件句柄，mode参数表示重定位的时机，有RTLD_NOW和RTLD_LAZY两个值 ，这个我们后面再分析是啥意思。

```cpp
void *dlsym( void *restrict handle, const char *restrict name );
``` 
    dlsym函数的功能是从这个新加载的库里根据传入的函数名，找到该函数的地址
```cpp
char *dlerror();
```
    dlerror没有入参，它会返回发生的上一个错误的字符串
```cpp
char *dlclose( void *handle );
```
    dlclose关闭打开的库文件，实际上如果当前还有其他的程序引用这个库，它是不会被关闭的，多个程序共享时采用引用计数机制，当最后一个引用它的程序关闭它时才会真正的关闭。这几个函数在使用时，需要包含<dlfcn.h>头文件。

下面我依然用之前sumapp这个简单的demo程序来演示一下动态加载库的用法。

    首先libsum.so生成方式不变，具体可参考上一篇文章。main.c的代码我们需要变一下，重新以dlopen的方式去引用libsum.so,  代码如下：
```cpp
192:zch kane$ ls
demodlopen.c    main.c        sum.h        sumappd
libsum.so    sum.c        sum.o
192:zch kane$ 
192:zch kane$ 
192:zch kane$ 
192:zch kane$ more demodlopen.c 
#include<stdio.h>
#include<dlfcn.h>

int  main(void)
{
    int iNum1 = 1;
    int iNum2 = 2;
    void * pHandle = NULL;
    int (*pFunc)(int, int) = NULL;
    char * pcError;
    int iRet = 0;

    pHandle = dlopen("libsum.so", RTLD_LAZY);
    if (NULL == pHandle)
    {
        printf("Open libsum.so failed!, error message is %s\r\n.", dlerror());
        return 0;
    }
    
    pFunc = dlsym(pHandle, "Sum");
    if (NULL == pFunc)
    {
        printf("Find function Sum failed!, error message is %s\r\n.", dlerror());
        return 0;
    }
    
    iRet =  (*pFunc)(iNum1, iNum2);
    printf("iRet = %d\r\n", iRet);
    dlclose(pHandle);
    return 0; 
}
192:zch kane$
```

编译demodlopen.c，执行sumappd

```shell
192:zch kane$ gcc -rdynamic -o sumappd demodlopen.c -ldl
192:zch kane$./sumappd 
iRet = 3
```
编译时-rdynamic用来通知链接器将所有符号添加到动态符号表中（目的是能够通过使用 dlopen 来实现向后跟踪）。-ldl 表明一定要将 dllib 链接于该程序。

总结：动态链接库在加载时机是进程启动，而动态加载库则由程序自行决定加载时机。看了下网上有一些同学也不太明白到底什么时候适合用动态链接库，什么时候适合用动态加载库。我说一下我在工作中遇到的一个场景就是用的动态加载。由于我们做的是基于linux的嵌入式软件平台，需要同时支持多款产品，而有些特性或者说业务，在某款产品上可能不支持，以达到产品差异化的目的。比如业务A在运行时可能会用到libabc.so, 但是 libabc.so在某款产品上可能会被裁减掉，因此对于这种情况，在编译版本及写代码的时候，就得用动态加载这种方式。因为如果你用动态链接的方式，而libabc.so刚好在这款产品上被裁掉了，会导致业务A的进程启动失败（动态链接在进程启动的时候去加载动态库失败）。

转载：[linux下的动态链接库和静态链接库到底是个什么鬼？（一）静态链接库的编译与使用](https://www.cnblogs.com/zhengchunhao/p/4881205.html)