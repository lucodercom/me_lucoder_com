---
title: "IONIC使用HTTP"
date: 2018-10-08T14:21:50+08:00
tags: ["IONIC","Angular","HTTP"]
categories: ["IONIC","Angular","HTTP"]
toc: false
---

# Http技术
http请求是客户端请求api使用的一项非常重要的技术，主要有下面几种方法。

# 使用Native组件
这种方法是一种和原生交互行最强的一种方法。

> 官网：https://ionicframework.com/docs/native/http/

```shell
$ ionic cordova plugin add cordova-plugin-advanced-http
$ npm install --save @ionic-native/http
```
<!-- more-->
具体用法：

```ts
import { HTTP } from '@ionic-native/http';

constructor(private http: HTTP) { }

this.http.get('http://ionic.io', {}, {})
  .then(data => {
    console.log(data.status);
    console.log(data.data); // data received by server
    console.log(data.headers);
  })
  .catch(error => {
    console.log(error.status);
    console.log(error.error); // error message as string
    console.log(error.headers);
  });
  ```
# 使用Angular自带的http组件（推荐）

1. 从 @angular/http 模块中导入 Http 类
2.  导入 RxJS 中的 map 操作符
3. 使用 DI 方式注入 http 服务
4. 调用 http 服务的 get() 方法，设置请求地址并发送 HTTP 请求
5. 调用 Response 对象的 json() 方法，把响应体转成 JSON 对象
6. 把请求的结果，赋值给 members 属性

```ts
import { Component, OnInit } from '@angular/core';
import { Http } from '@angular/http'; // (1)
import 'rxjs/add/operator/map'; // (2)
interface Member {
  id: string;
  login: string;
  avatar_url: string;
}
@Component({
  selector: 'exe-app',
  template: `
    <h3>Angular Orgs Members</h3>
    <ul *ngIf="members">
      <li *ngFor="let member of members;">
        <p>
          <img [src]="member.avatar_url" width="48" height="48"/>
          ID：<span>{{member.id}}</span>
          Name: <span>{{member.login}}</span>
        </p>
      </li>
    </ul>
  `
})
export class AppComponent implements OnInit {
  members: Member[];
  constructor(private http: Http) { } // (3)
  ngOnInit() {
    this.http.get(`https://api.github.com/orgs/angular/members?page=1&per_page=5`) // (4)
      .map(res => res.json()) // (5)
      .subscribe(data => {
        if (data) this.members = data; // (6)
      });
  }
}
```

具体内容和步骤请到这里查看：[https://segmentfault.com/a/1190000009028150?utm_source=tuicool&utm_medium=referral#articleHeader2](https://segmentfault.com/a/1190000009028150?utm_source=tuicool&utm_medium=referral#articleHeader2)

# 使用JQuery的http组件（不太推荐的方法）

如果你不熟悉上述的方法，也可以使用JQuery，`但是我们并不推荐使用这种方法`，ionic是混合开发的框架，当然兼容JQuery。

1. 引入JQuery脚本
2. 使用JQuery 的Ajax
