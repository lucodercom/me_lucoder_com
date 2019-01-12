---
title: "动态网站模块错误"
date: 2018-02-27T14:21:50+08:00
tags: ["Java","Javaweb","模块错误"]
categories: ["Java","Javaweb","模块错误"]
toc: false
---

# 动态网站模块错误

![错误](https://oss.lucoder.com/uploads/2018/02/27/201802272217.png)

```
Dynamic Web Module 3.0 Require 1.6 Or Newer。
```
修改`pom.xml`
```xml
<build>  
        <plugins>  
            <plugin>  
                <groupId>org.apache.maven.plugins</groupId>  
                <artifactId>maven-compiler-plugin</artifactId>  
                <version>3.1</version>  
                <configuration>  
                    <source>1.8</source>  
                    <target>1.8</target>  
                </configuration>  
            </plugin>  
        </plugins>  
    </build>
```