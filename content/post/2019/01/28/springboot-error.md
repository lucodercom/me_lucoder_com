---
title: "Spring Boot报错 No goals have been specified for this build"
date: 2019-01-22T23:16:23+08:00
draft: false
tags: ["SpringBoot", "Maven", "Java"]
categories: ["Java", "Spring Boot"]
toc: false
---

# 错误

废话不多，直接来图！

![https://oss.lucoder.com/uploads/2019/01/28/20190128220558.png](https://oss.lucoder.com/uploads/2019/01/28/20190128220558.png)

# 解决办法

`pom.xml`文件<build>标签里面最后面添加。
```
<defaultGoal>compile</defaultGoal>  
```