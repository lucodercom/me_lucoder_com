---
title: "树莓派Python版本更换"
date: 2019-01-12T15:58:23+08:00
draft: false
tags: ["树莓派", "嵌入式", "Python"]
categories: ["嵌入式","树莓派", "Python"]
toc: false
---

树莓派自带Python2和Python3，其中默认是Python2，使用Python3的方法是Python3和Pip3等，由于Python2的种种原因，我计划干掉Python2，只用Python3.

1. 删除Python2.7

```
sudo apt-get autoremove python2.7
```

2. 链接python

```
sudo ln -s python3.5 python
sudo ln -s pip3 pip
```

3. 检测

```
python
```

4. 后期处理

```
sudo apt-get update
sudo apt-get upgrade
```