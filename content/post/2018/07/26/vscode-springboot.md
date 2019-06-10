---
title: "Vscode 调试Spring Boot程序"
date: 2018-07-26T14:21:50+08:00
tags: ["环境搭建","Javaweb","Spring Boot"]
categories: ["Java","Javaweb","Spring Boot","Vscode","调试"]
toc: false
---

# 摘要

![Spring Boot](https://upload-images.jianshu.io/upload_images/7896890-9323041dd0ce883e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>Build Anything with Spring Boot：Spring Boot is the starting point for building all Spring-based applications. Spring Boot is designed to get you up and running as quickly as possible, with minimal upfront configuration of Spring.

<!--more -->

大概意思翻译一下：Spring Boot 是所有基于 Spring 开发的项目的起点。Spring Boot 的设计是为了让你尽可能快的跑起来 Spring 应用程序并且尽可能减少你的配置文件。这些都不再多说，Java 的Spring Boot我个人来说还是比较看好的。

Visual Studio Code (简称 VS Code / VSC) 是一款免费开源的现代化轻量级代码编辑器，支持几乎所有主流的开发语言的语法高亮、智能代码补全、自定义热键、括号匹配、代码片段、代码对比 Diff、GIT 等特性，支持插件扩展，并针对网页开发和云端应用开发做了优化。软件跨平台支持 Win、Mac 以及 Linux。他最强大的地方是微软的`Language Server`，这货可以通过插件的方法支持`Java`、`C#`、`Python`、`Typescript`、`PHP`....

说了这么多的废话，开始引出本文的重点：`Spring Boot调试`。

# 插件安装

关于VSCode 参考这篇文章：[Visual Studio Code](/post/201711/visual-studio-code/)

在使用之前确保你已经安装了Java环境、安装了Vscode，然后安装下面的几个插件，至少需要这么几个插件：
```
Java 环境：
Java Extension Pack
Java Language Support
Debuger for Java

Spring boot 环境：
Spring Boot Extension Pack
Spring Boot Tools
Spring Initializr Java Support

Maven 环境：
Maven for Java

```

# 生成项目

开始生成项目：

`Ctrl`+`Shift`+`P`，输入`Spring`，选择`Maven项目`（我个人比较喜欢Maven），然后选择`Java`，输入项目名称假设为`com.mx`，然后输入项目名称假设为`spider`，然后我一般选择`2.0.3`，由于是测试项目，我只选择一个`DevTools`，然后选择目录生成项目，然后打开项目`Open it`。

需要注意一点，项目生成之后请先运行一下maven指令，下载必要的数据包。

然后下面就是激动人心的时刻了！

# 撸代码

测试代码是一个简单爬虫程序，爬取博客园博客数据。

首先在`pom.xml`文件中添加我们用到的包`jsoup`：
```xml
<dependency>
  <!-- jsoup HTML parser library @ https://jsoup.org/ -->
  <groupId>org.jsoup</groupId>
  <artifactId>jsoup</artifactId>
  <version>1.11.3</version>
</dependency>
```

然后我们首先找到`src/main/java/com/mx/spider/DemoApplication.java`文件，修改Spring Boot的启动行。
```Java
SpringApplication.run(DemoApplication.class, args);//删除启动项
System.out.println("Hello world!");
```
然后点击调试`蜘蛛图标`，直接点击调试(注意直接点击，先别配置，使用Vscode默认配置足以！)，首次将会非常慢。结合我的原因，应该是`maven`下载数据包和初始化引擎所致。

# 代码调试

点击运行，妈耶！好神奇，环境好了！

![Hello world](https://oss.lucoder.com/uploads/2018/07/26/20180726165210.png)

# 来点复杂的调试

下面来点复杂的调试，首先我们创建一个模型Blog，一个控制器BlogSpiderController，然后开始下面的工作：

```Java
package com.mx.models;

public class Blog {
    private String _url;
    private String _title;
    private String _detail;

    public String getUrl() {
        return _url;
    }

    public String getTitle() {
        return _title;
    }

    public String getDetail() {
        return _detail;
    }

    public void setUrl(String url) {
        if (url.length() > 0 && (url.startsWith("http://") || url.startsWith("https://")))
            _url = url;
    }

    public void setTitle(String title) {
        if (title.length() > 0)
            _title = title;
    }

    public void setDetail(String detial) {
        if (detial.length() > 0)
            _detail = detial;
    }
}
```

```Java
package com.mx.controllers;

import java.util.ArrayList;
import java.util.List;

import com.mx.models.Blog;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

public class BlogSpiderController {
    public void Run() throws Exception {
        Document doc = null;

        for (int i = 1; i <= 10; i++) {
            doc = getUrlDocument("https://www.cnblogs.com/#p" + i);

            for (Blog blog : getBlogListByDocument(doc)) {
                bindBlogDetail(blog);
                System.out.println(blog);
            }
        }
        System.out.println("Task Finished!");
    }

    public Document getUrlDocument(String url) throws Exception {
        Document doc = Jsoup.connect(url).get();
        return doc;
    }

    public List<Blog> getBlogListByDocument(Document doc) throws Exception{
        List<Blog> list = new ArrayList<Blog>();
        Element pageList = doc.getElementById("post_list");
        Elements blogs = pageList.getElementsByClass("post_item");

        Blog b;
        for (Element blog : blogs) {
            b = new Blog();
            b.setTitle(blog.getElementsByTag("h3").text());
            b.setUrl(blog.getElementsByClass("titlelnk").get(0).attr("href"));
            b.setSumary(blog.getElementsByClass("post_item_summary").text());
            list.add(b);
        }

        return list;
    }

    public void bindBlogDetail( Blog blog) throws Exception{
        //Do data bind
    }
}
```

好了代码部分先写这么多，上文中绑定详细信息部分我不再写，因为几个步骤已经满足调试需求。

# 断点调试

1. 按照vs习惯，下设断点。

同样的，在代码左侧双击下设断点，在断点监视器也可以随时修改断点。

![断点设置](https://oss.lucoder.com/uploads/2018/07/26/20180726165304.png)


2. 点击调试

我的习惯是查看这几部分：
	1). 变量属性
	2). 堆栈调用情况
	3). 变量变化情况

分别从下面几块儿查看数据。

![变量域](https://oss.lucoder.com/uploads/2018/07/26/20180726165432.png)

3. 查看调试信息

![调试信息](https://oss.lucoder.com/uploads/2018/07/26/20180726165411.png)

![调试信息](https://oss.lucoder.com/uploads/2018/07/26/20180726165503.png)

> 总结

![程序结果](https://oss.lucoder.com/uploads/2018/07/26/20180726165607.png)

本文主要使用Spring Boot Console程序演示Spring Boot的调试过程，但是Spring Boot如果是面向于Web端或许更加合适，但是Web代码编写相对复杂，所以选用了Console来演示调试过程。

【源代码】：[YunFile](http://skpan.com/fs/4lfu0c7oad5e9r2com3/)