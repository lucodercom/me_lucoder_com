---
title: "ionic 融入百度地图"
date: 2018-04-24T14:21:50+08:00
tags: ["IONIC","定位","百度地图"]
categories: ["定位","百度地图"]
toc: false
---

# ionic 融入百度地图
百度地图是一个非常成熟的一个地图组件，在很多项目中都会用到百度地图组件，本节就介绍如何在ionic项目中使用百度地图。

## 在index.html中添加引用
在ionic的`index.html`文件中添加script引用脚本：
```html
<script src="https://api.map.baidu.com/api?v=2.0&ak=da1K4Mxg4LItgEhMy5h8k3ZbXzLzFb2I" type="text/javascript"></script>
```
## 引入百度地图
```ts
import { Component } from '@angular/core';
import {Platform, IonicPage, NavController, NavParams } from 'ionic-angular';

//百度地图API需要先这样声明一下，声明以后就可以使用了
declare var BMap: any;

@IonicPage()
@Component({
  selector: 'page-map-demos',
  templateUrl: 'map-demos.html',
})
export class MapDemosPage {
  constructor(public navCtrl: NavController,public navParams: NavParams) {
  }

  ionViewDidLoad() {
    // 创建百度地图API的Map实例
    let map = new BMap.Map('map');
    // 初始化地图,设置中心点坐标（河大计算机学院门前路上）和地图级别（11）
    map.centerAndZoom(new BMap.Point(114.315745, 34.824635), 11);
    //添加地图类型控件
    map.addControl(new BMap.MapTypeControl());
    // 设置地图显示的城市 此项是必须设置的
    map.setCurrentCity("开封");
    //开启鼠标滚轮缩放，默认false
    map.enableScrollWheelZoom(true);
  }
}

```
# 结果

![结果1](https://oss.lucoder.com/uploads/2018/04/24/201804240803.png)

![结果2](https://oss.lucoder.com/uploads/2018/04/24/201804240806.png)
