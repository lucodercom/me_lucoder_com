---
title: "基于LocalSortage实现的web临时数据库"
date: 2018-04-21T14:21:50+08:00
tags: ["前端","H5DB","LocalSortage"]
categories: ["前端","H5DB","LocalSortage","数据库"]
toc: false
---

# 基于LocalSortage实现的web临时数据库

> 该数据库基于localStorage的一个封装，实现键值对的存入和键值的取出，支持跨页面数据保存。该封装是团队内部使用的封装，不保证通用性以及bug的出现。

<!--more-->

# 用途
可以用于临时保存登陆用户的数据，不必重复回发请求服务器。

# Javscript代码
```javascript
//本地数据库
var LocalDb = function () {
    var db = [];
    this.print = function () {
        console.log(db);
    }
    this.add = function (key, value) {
        if (this.exist(key)) {
            console.log("键入重复数据！");
            return;
        }
        var json = { "key": key, "value": value }
        db.push(json);
    }
    this.remove = function (key) {
        var index = -1;
        for (var i = 0; i < db.length; i++) {
            if (db[i].key == key) {
                index = i;
            }
        }

        if (index == -1) return;
        else db[index] = null;
    }
    this.get = function (key) {
        for (var i = 0; i < db.length; i++) {
            if (db[i].key == key) return db[i].value;
        }
    }
    this.index = function (index) {
        return db[index];
    }
    this.exist = function (key) {
        for (var i = 0; i < db.length; i++) {
            if (db[i].key == key) return true;
        }
    }
    this.load = function (name) {
        if (window.localStorage) {//判断浏览器是否支持 localStorage
            var ls = window.localStorage;
            db = JSON.parse(ls.getItem("mx_web_db_sortager"));//设置值
        } else {
            console.log('浏览器不支持 localStorage');
        }
    }
    this.write = function (name, expiredays) {
        if (window.localStorage) {//判断浏览器是否支持 localStorage
            var ls = window.localStorage;
            ls.setItem("mx_web_db_sortager", JSON.stringify(db));//设置值
        } else {
            console.log('浏览器不支持 localStorage');
        }
    }

}
```

# 未来扩展
使用dom的cookie保存数据，可以保证较长时间的存储。