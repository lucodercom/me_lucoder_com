---
title: "使用Github建立博客系统"
date: 2018-10-01T16:01:23+08:00
draft: false
tags: ["Github","Travis-ci","hexo","hugo"]
categories: ["Github"]

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
# comment: false
toc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
# contentCopyright: false
# reward: false
# mathjax: false
---

# 说明

`Github Pages`是一个免费的静态页面托管平台，很多人都基于`Github Page` 建立独立博客，笔者也是很早就开始建立个人博客，但是原先是基于`Typecho`的程序，但是`Typecho`是有PHP写的，所以就需要付出服务器成本，而`Github Page`则是一个免费的，所以博主的文章将会慢慢移植到`Github Page`上面。

<!-- more -->

# 前提条件

建立博客之前首先确保下面的环境是否部署完成！

## Node 环境

`Node JS` Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 Node.js 的包管理器 npm，是全球最大的开源库生态系统。

### 下载

点击下面的下载地址下载NODE，根据经验建议使用压缩包版本，因为安装包版本会将一部分内容装到系统盘，后期安装插件和软件将会涉及到权限问题。

下载地址：[http://nodejs.cn/download/](http://nodejs.cn/download/)

### 配置

首先配置环境变量，配置环境变量和其他软件配置方法是一样的，就是在`PATH`中填充相关的内容。如果你是安装的安装包可能会自动配置环境变量，如果是压缩包只需要配置到下面的一层即可。

```shll
D:\Program Files\node-v10.8.0-win-x64
```

> 注意：配置的地址自己修改，只需要看到`node.exe`文件这一层即可。

### 配置NPM

NPM在国内部分地区可能会比较缓慢，可以使用[淘宝的npm](https://npm.taobao.org/)，配置完成之后只需要将`npm`改用`cnpm`即可。

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

### 安装Hexo

安装hexo的命令行。
```shell
npm install hexo-cli -g
```

## Git

如果作为一个开发者不会使用Git可能说出去不好听，官网在这[https://git-scm.com/](https://git-scm.com/)，照样配置环境变量，在此处不在赘述。

## Visual Studio Code

Microsoft在2015年4月30日Build 开发者大会上正式宣布了 Visual Studio Code 项目：一个运行于Mac OS X、Windows和 Linux 之上的，针对于编写现代 Web 和云应用的跨平台源代码编辑器。Visual Studio Code可以认为是一个Mini版本的Visual Studio，功能相当强大！

下载地址：[https://code.visualstudio.com/?wt.mc_id=vscom_downloads](https://code.visualstudio.com/?wt.mc_id=vscom_downloads)

Visual Studio Code 可以认为是一个普通的软件，你可以直接下一步下一步安装软件。软件安装完成之后打开界面就是这样的软件：

![https://oss.lucoder.com/uploads/2018/10/01/20181001212314.png](https://oss.lucoder.com/uploads/2018/10/01/20181001212314.png)

至此为止，项目初始条件结束。

# 仓库和使用

如果你不会在`Github`创建仓库，那你不要看本文了，不适合你。如果你使用国内的`Gitee`、`Coding`等自己搜索一些相关资料，本篇使用的是`Github`，虽然速度有时不太理想，但是免费并且没有广告，最让人欣喜的是绑定自定义域名并且可以让域名支持https。

## 前提条件

前提需要你已经创建一个空白仓库`myblog`，给仓库创建一个分支叫做`blog_src`，然后才可以进行下面的步骤。

### 设置Github Page

首先要开启`Github Page`功能，选择`master branch`，然后如果你有独立域名就在下面框中输入你的域名。

![https://oss.lucoder.com/uploads/2018/10/01/20181001213405.png](https://oss.lucoder.com/uploads/2018/10/01/20181001213405.png)

### 获取TOKEN

获取你的`Github`的`Access Token`，可以`push`到我们的分支。

首先选择`设置` --> `Developer Settings` --> `Personal Token` --> `Generate new token`，然后给你的`Token`起一个名字，然后下面的权限建议除了删除(`delete_repo`) 其他的都打开，开放给工具最大的权限，但是为了防止操作的失误，所以删除了删除权限。

![https://oss.lucoder.com/uploads/2018/10/01/20181001213636.png](https://oss.lucoder.com/uploads/2018/10/01/20181001213636.png)

![https://oss.lucoder.com/uploads/2018/10/01/20181001213658.png](https://oss.lucoder.com/uploads/2018/10/01/20181001213658.png)

![https://oss.lucoder.com/uploads/2018/10/01/20181001213741.png](https://oss.lucoder.com/uploads/2018/10/01/20181001213741.png)

![https://oss.lucoder.com/uploads/2018/10/01/20181001213754.png](https://oss.lucoder.com/uploads/2018/10/01/20181001213754.png)

### 创建博客程序

首先从你的仓库克隆代码，`注意`：建议只克隆你的源码仓库，因为你只需要操作源码仓库即可，无需关心`master`仓库，`master`仓库由部署工具自动`push`的。

```
git clone -b blog-dev https://github.com/xxxxxxxxx
```

然后控制台进入到仓库，输入下面指令创建博客程序：

```shell
hexo init
cnpm install
hexo server
```

启动之后，打开浏览器输入地址：`http://localhost:4000`打开网站查看是否成功，如果可以正常打开网站，那么就可以继续下面的步骤，否则检查错误直到正常打开网站。

###  Travis-ci

Travis-ci是一个免费的自动部署平台，支持用户编写脚本自动部署程序，官网是：[https://travis-ci.org/](https://travis-ci.org/)。

首先你使用`Github`登录到网站：

![https://oss.lucoder.com/uploads/2018/10/01/20181001215042.png](https://oss.lucoder.com/uploads/2018/10/01/20181001215042.png)

登陆之后平台将会自动加载你的仓库，然后选择你的博客仓库，打开即可。

![https://oss.lucoder.com/uploads/2018/10/01/20181001215701.png](https://oss.lucoder.com/uploads/2018/10/01/20181001215701.png)

配置一下你的`TOKEN`环境变量，首先点击设置按钮：

![https://oss.lucoder.com/uploads/2018/10/01/20181001215930.png](https://oss.lucoder.com/uploads/2018/10/01/20181001215930.png)

添加你的环境变量，名和值，名字自己起，值就是你在上面`Github`生成的`TOKEN`。

![https://oss.lucoder.com/uploads/2018/10/01/20181001220019.png](https://oss.lucoder.com/uploads/2018/10/01/20181001220019.png)


# 开始部署

现在万事俱备，只欠东风。东风是什么，就是我们`Travis-ci`的配置脚本，我是用的脚本如下：



```yml
language: node_js
node_js: stable

# S: Build Lifecycle
install:
  - npm install

#before_script:
 # - npm install -g gulp

script:
  - hexo g

after_script:
  - cd ./public
  - echo "blog.lucoder.com" > CNAME #你绑定的域名
  - git init
  - git config user.name "lucodercom"
  - git config user.email "me@lucoder.com"
  - git add .
  - git commit -m "修改主页面"
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master #{这里注意两个变量，一个是 GH_TOKEN就是Travis-ci中的环境变量，并且我传到了master分支，你根据需要修改，注意不要把源码和发布放一个分支！！}

branches:
  only:
    - blog_src
env:
 global:
   - GH_REF: github.com/lucodercom/my_lucoder_com.git #{这里是你的仓库地址，注意不要加https！！！}
```

把文件保存为`.travis.yml`，保存到你的博客根目录。其实你估计遗精看明白了，配置文件主要部分就是`script`和`after_script`。

# 提交

此时你用`Visual Studio Code` 打开文件夹，在最左侧一栏，第三个按钮可以看到有提交的信息，输入提交信息之后，点击对号提交。提交完毕之后点击三个点那个按钮，推送到分支，输入你的用户名和密码即可。

# 等待部署

此时你进入到`Travis-ci`，可以查看部署情况。

![https://oss.lucoder.com/uploads/2018/10/01/20181001220950.png](https://oss.lucoder.com/uploads/2018/10/01/20181001220950.png)

![https://oss.lucoder.com/uploads/2018/10/01/20181001220933.png](https://oss.lucoder.com/uploads/2018/10/01/20181001220933.png)


部署完毕之后你可以到仓库查看提交情况，域名解析成功之后就可以访问域名了。

## 附：

### Hugo 的自动化配置文件

```yml
# https://docs.travis-ci.com/user/deployment/pages/
# https://docs.travis-ci.com/user/languages/python/
language: python

python:
    - "3.6"

install:
    
script:
    - uname -a
    - sudo apt-get install -y libstdc++6 
    - sudo apt-get install -y lib32stdc++6
    #- wget https://github.com/gohugoio/hugo/releases/download/v0.50/hugo_0.50_Linux-32bit.deb
    - wget https://github.com/gohugoio/hugo/releases/download/v0.50/hugo_0.50_Linux-64bit.deb
    - sudo dpkg -i hugo*.deb
#    - rm -rvf ./*
    - mkdir rawfile
    - pwd
    - cd rawfile
    - pwd
    - git config --global user.email "me@lucoder.com"
    - git config --global user.name "lucodercom"
    - git clone -b rawfile https://github.com/lucodercom/me_lucoder_com.git
    - ls
    - cd me_lucoder_com
    - ls
    - mkdir public
    - pwd
    - ls
    - hugo -d public
    - ls
    - echo 'Hugo build done!'
    - git clone https://$GH_TOKEN@github.com/lucodercom/me_lucoder_com.git master
    - cd master 
    - rm -rf ./*
    - cp -r ../public/* ./
    - echo 'me.lucoder.com' > CNAME
    - git add .
    - git commit -m 'Travis upate blog'
    - git push -u origin master

after_success:
    - ls
```

# 结束语

使用这几个工具非常方便，`Visual Studio Code`支持`Markdown`并且对Git操作基本都是图形化操作，非常方便，使用` Travis-ci `可以在云端自动部署，你不必每次都写完文章在发布一次然后推送源码这么繁杂。