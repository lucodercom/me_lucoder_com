---
title: "设置Maven镜像加快文件下载"
date: 2018-10-01T14:21:50+08:00
tags: ["Java","Maven","镜像"]
categories: ["Java","Maven","镜像"]
toc: false
---

# 设置Maven镜像加快文件下载

> 在日常生活中，我们使用maven下载需要的jar包，但是很多的时候由于中央仓库没有，所以我们没有办法下载到需要的jar包，手动去下载上，然后放入到lib下，然后build path有的时候会感到很不舒服，不是很是不实用。所以此处可以在maven的设置中心添加多个下载仓库，当中央仓库没有的话，继续到下一个仓库去下载。这样丰富了中央仓库的下载地址。

首先找到你的Maven的配置文件的位置，然后将下面的代码复制到你的配置文件中：

> 注意将速度比较慢的网址放到后面

```xml
<mirrors>  
    <!-- mirror  
     | Specifies a repository mirror site to use instead of a given repository. The repository that  
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used  
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.  
     |  
    <mirror>  
      <id>mirrorId</id>  
      <mirrorOf>repositoryId</mirrorOf>  
      <name>Human Readable Name for this Mirror.</name>  
      <url>http://my.repository.com/repo/path</url>  
    </mirror>  
     -->  

     <mirror>
        <id>nexus-163</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus 163</name>
        <url>http://mirrors.163.com/maven/repository/maven-public/</url>
    </mirror>
	<mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
    <mirror>
        <id>central</id>
        <name>Maven Repository Switchboard</name>
        <url>http://repo1.maven.org/maven2/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
    <mirror>
        <id>repo2</id>
        <mirrorOf>central</mirrorOf>
        <name>Human Readable Name for this Mirror.</name>
        <url>http://repo2.maven.org/maven2/</url>
    </mirror>
    <mirror>
        <id>ibiblio</id>
        <mirrorOf>central</mirrorOf>
        <name>Human Readable Name for this Mirror.</name>
        <url>http://mirrors.ibiblio.org/pub/mirrors/maven2/</url>
    </mirror>
    <mirror>
        <id>jboss-public-repository-group</id>
        <mirrorOf>central</mirrorOf>
        <name>JBoss Public Repository Group</name>
        <url>http://repository.jboss.org/nexus/content/groups/public</url>
    </mirror>
    <!-- 中央仓库在中国的镜像 -->
    <mirror>
        <id>maven.net.cn</id>
        <name>oneof the central mirrors in china</name>
        <url>http://maven.net.cn/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>  
```