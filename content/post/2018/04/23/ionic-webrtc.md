---
title: "ionic 使用 webrtc"
date: 2018-04-23T14:21:50+08:00
tags: ["IONIC","视频通讯","WebRTC"]
categories: ["WebRTC"]
toc: false
---
# ionic 使用 webrtc

> WebRTC实现了基于网页的视频会议，标准是WHATWG 协议，目的是通过浏览器提供简单的javascript就可以达到实时通讯（Real-Time Communications (RTC)）能力。
WebRTC（Web Real-Time Communication）项目的最终目的主要是让Web开发者能够基于浏览器（Chrome\FireFox\...）轻易快捷开发出丰富的实时多媒体应用，而无需下载安装任何插件，Web开发者也无需关注多媒体的数字信号处理过程，只需编写简单的Javascript程序即可实现，W3C等组织正在制定Javascript 标准API，目前是WebRTC 1.0版本，Draft状态；另外WebRTC还希望能够建立一个多互联网浏览器间健壮的实时通信的平台，形成开发者与浏览器厂商良好的生态环境。同时，Google也希望和致力于让WebRTC的技术成为HTML5标准之一，可见Google布局之深远。

WebRTC提供了视频会议的核心技术，包括音视频的采集、编解码、网络传输、显示等功能，并且还支持跨平台：windows，linux，mac，android。 --- [百度百科](https://baike.baidu.com/item/WebRTC/5522744?fr=aladdin)

官网：[www.webrtc.org](www.webrtc.ort)

# 实现QQ电话功能

本节介绍如何使用ionic + webrtc使用rtcmulticonnection组件实现QQ电话功能。

官网：[http://www.rtcmulticonnection.org/](http://www.rtcmulticonnection.org/)

Github：[https://github.com/muaz-khan/RTCMultiConnection](https://github.com/muaz-khan/RTCMultiConnection)

## 引用脚本

```html
  <!-- WebRTC 引用 -->
  <script src="https://rtcmulticonnection.herokuapp.com/dist/RTCMultiConnection.min.js"></script>
  <script src="https://rtcmulticonnection.herokuapp.com/socket.io/socket.io.js"></script>
  <!-- <script src="assets/js/getHtmlMediaElement.js"></script> -->
  <script src="assets/js/getMediaElement.js"></script>
```

## html代码

```html
<ion-header>
  <ion-navbar>
    <ion-title>视频聊天界面</ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <div id="videos-container"></div>
</ion-content>

```

## Typescript代码

```ts
import { Component } from '@angular/core';
import { IonicPage, NavController, NavParams } from 'ionic-angular';
declare var RTCMultiConnection;
declare var MediaHelper;

@IonicPage()
@Component({
  selector: 'page-video-running',
  templateUrl: 'video-running.html',
})
export class VideoRunningPage {

  mediaHelper: any;
  connection: any;
  constructor(public navCtrl: NavController, public navParams: NavParams) {
  }

  ionViewDidLoad() {
    this.mediaHelper = new MediaHelper();
    this.connection = new RTCMultiConnection();
    this.doWork();
  }

  doWork(): void {
    //数据配置
    this.connection.socketURL = 'https://rtcmulticonnection.herokuapp.com:443/';
    // comment-out below line if you do not have your own socket.io server
    // connection.socketURL = 'https://rtcmulticonnection.herokuapp.com:443/';
    this.connection.socketMessageEvent = 'video-conference-demo';
    this.connection.session = {
      audio: true,
      video: true
    };
    this.connection.maxParticipantsAllowed = 1;
    this.connection.sdpConstraints.mandatory = {
      OfferToReceiveAudio: true,
      OfferToReceiveVideo: true
    };
    this.connection.videosContainer = document.getElementById('videos-container');

    this.connection.onstream = (event) => {
      if (event.type == 'local') {
        event.stream.muted = true;
      }

      var width = 200;//parseInt(connection.videosContainer.clientWidth / 2) - 20;
      var mediaElement = this.mediaHelper.getMedia(event.mediaElement, {
        title: event.userid,
        buttons: ['full-screen'],
        width: width,
        showOnMouseEnter: false
      });
      this.connection.videosContainer.appendChild(mediaElement);
      setTimeout(() => {
        mediaElement.media.play();
      }, 5000);
      mediaElement.id = event.streamid;
    };

    this.connection.onstreamended = (event) => {
      var mediaElement = document.getElementById(event.streamid);
      if (mediaElement) {
        mediaElement.parentNode.removeChild(mediaElement);
      }
    };
    this.connection.openOrJoin("15700000000", (isRoomExists, roomid) => {
      if (!isRoomExists) {
        console.log(roomid);
      }
    });
  }
}
```

# 效果

![效果](https://oss.lucoder.com/uploads/2018/04/23/201804231103.png)