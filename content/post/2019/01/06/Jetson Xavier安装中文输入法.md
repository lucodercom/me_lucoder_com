---
title: "Jetson Xavier 安装中文输入法"
date: 2019-01-06T22:01:23+08:00
draft: false
tags: ["机器视觉", "Opencv", "图像处理"]
categories: ["机器视觉", "Opencv"]
toc: false
---

我实验结果是只有谷歌输入法可以用，并且用户体验不错。

一共两个步骤，首先更新软件园，然后安装谷歌拼音。

```shell
# update software repertories
sudo apt-get update

# fix google pin yin
sudo apt-get install fcitx-googlepinyin
```

![https://oss.lucoder.com/uploads/2019/01/06/20190106060423.png](https://oss.lucoder.com/uploads/2019/01/06/20190106060423.png)

在settings -> language support 里将  keyboard input method system 设置为fcitx，然后关闭软件，重启板子。

![https://oss.lucoder.com/uploads/2019/01/06/20190106060518.png](https://oss.lucoder.com/uploads/2019/01/06/20190106060518.png)

在settings-> Text Entry 里，添加输入源， 搜索 google-pinyin，添加。

![https://oss.lucoder.com/uploads/2019/01/06/20190106061227.png](https://oss.lucoder.com/uploads/2019/01/06/20190106061227.png)


PS : 按Ctrl +Space 可以中英文输入切换
