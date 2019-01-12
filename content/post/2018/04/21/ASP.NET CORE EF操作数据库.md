---
title: "ASP.NET CORE EF操作数据库"
date: 2018-04-02T14:21:50+08:00
tags: ["ASP.NET Core","EF Core"]
categories: ["ASP.NET Core","EF Core"]
toc: false
---

> 写在前面：本文主要内容大多参考的MSD官网文档，本节参考网址 [https://docs.microsoft.com/zh-cn/aspnet/core/data/ef-rp/intro](https://docs.microsoft.com/zh-cn/aspnet/core/data/ef-rp/intro)

# CodeFirst

## 安装EF框架

```Powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
```
<!--more-->
## 创建Model实体
```C#
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }

    public ICollection<Enrollment> Enrollments { get; set; }
}
public class Course
{
    [DatabaseGenerated(DatabaseGeneratedOption.None)]
    public int CourseID { get; set; }
    public string Title { get; set; }
    public int Credits { get; set; }
    public ICollection<Enrollment> Enrollments { get; set; }
}
public enum Grade
{
    A, B, C, D, F
}
public class Enrollment
{
    public int EnrollmentID { get; set; }
    public int CourseID { get; set; }
    public int StudentID { get; set; }
    public Grade? Grade { get; set; }
    public Course Course { get; set; }
    public Student Student { get; set; }
}
```
## 创建数据上下文

```C#
public class SchoolContext : DbContext
    {
        public SchoolContext(DbContextOptions<SchoolContext> options) : base(options)
        {
        }

        public DbSet<Course> Courses { get; set; }
        public DbSet<Enrollment> Enrollments { get; set; }
        public DbSet<Student> Students { get; set; }
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Course>().ToTable("Course");
            modelBuilder.Entity<Enrollment>().ToTable("Enrollment");
            modelBuilder.Entity<Student>().ToTable("Student");
        }
    }
```
## 依赖注入

打开`Setup.cs`文件

```C#
services.AddDbContext<SchoolContext>(options =>
      options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
```

## 创建连接字符串

打开`appsettings.json`文件
```JSON
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity1;ConnectRetryCount=0;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  }
}
```

## 创建数据库

打开`工具` -> `NuGet包管理` -> `包管理控制台`

```Powershell
 Add-Migration InitialCreate
 Update-Database
```

如果过程中出现错误：`The term 'add-migration' is not recognized as the name of a cmdlet`，重启Visual Studio解决。

## 创建控制器
1. 在`解决方案资源管理器`中，右键单击`控制器`文件夹，然后选择`添加`>`控制器`。
2. 选择`视图使用 Entity Framework 的 MVC 控制器`，然后单击`确定`。
3. 将`模型类`设置为`Student`，将`数据上下文类`设置为 `StudentContext`。
4. 单击 `添加`。

选中`wwwroot`右键浏览器查看，输入你的控制器和地址即可，或者直接点击运行。

# DbFirst

## 安装EF框架

```Powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

## 初始化数据库
```SQL
CREATE DATABASE [Blogging];
GO

USE [Blogging];
GO

CREATE TABLE [Blog] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NOT NULL,
    CONSTRAINT [PK_Blog] PRIMARY KEY ([BlogId])
);
GO

CREATE TABLE [Post] (
    [PostId] int NOT NULL IDENTITY,
    [BlogId] int NOT NULL,
    [Content] nvarchar(max),
    [Title] nvarchar(max),
    CONSTRAINT [PK_Post] PRIMARY KEY ([PostId]),
    CONSTRAINT [FK_Post_Blog_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blog] ([BlogId]) ON DELETE CASCADE
);
GO

INSERT INTO [Blog] (Url) VALUES
('http://blogs.msdn.com/dotnet'),
('http://blogs.msdn.com/webdev'),
('http://blogs.msdn.com/visualstudio')
GO
```
## 反向工程生成代码

`工具`–>`NuGet 包管理器`–>`包管理器控制台`

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

如果遇到错误`The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`，请重启Visual Studio！

## 依赖注入

打开`Setup.cs`文件

```C#
services.AddDbContext<SchoolContext>(options =>
      options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
```

## 创建连接字符串

打开`appsettings.json`文件
```JSON
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity1;ConnectRetryCount=0;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  }
}
```

## 创建控制器
1. 在`解决方案资源管理器`中，右键单击`控制器`文件夹，然后选择`添加`>`控制器`。
2. 选择`视图使用 Entity Framework 的 MVC 控制器`，然后单击`确定`。
3. 将`模型类`设置为`Blogs`，将`数据上下文类`设置为 `BlogsContext`。
4. 单击 `添加`。

选中`wwwroot`右键浏览器查看，输入你的控制器和地址即可，或者直接点击运行。

# 结果

![运行结果1](https://docs.microsoft.com/zh-cn/ef/core/get-started/aspnetcore/_static/create.png)

![运行结果2](https://docs.microsoft.com/zh-cn/ef/core/get-started/aspnetcore/_static/index-existing-db.png)