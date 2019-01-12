---
title: "ionic3构建过程中遇到找不到AndroidManifest.xml"
date: 2018-10-11T14:21:50+08:00
tags: ["IONIC","Cordova","AndroidManifest"]
categories: ["IONIC","Cordova","AndroidManifest"]
toc: false
---

构建Ionic时候有时候会遇到下面问题：

```error
Failed to install 'ionic-plugin-keyboard': Error: ENOENT: no such file or directory, open '/Users/theo/Projects/mobile/platforms/android/AndroidManifest.xml'
    at Object.fs.openSync (fs.js:651:18)
    at Object.fs.readFileSync (fs.js:553:33)
    at Object.parseElementtreeSync (/Users/theo/Projects/mobile/platforms/android/cordova/node_modules/cordova-common/src/util/xml-helpers.js:180:27)
    at new AndroidManifest (/Users/theo/Projects/mobile/platforms/android/cordova/lib/AndroidManifest.js:29:20)
    at AndroidProject.getPackageName (/Users/theo/Projects/mobile/platforms/android/cordova/lib/AndroidProject.js:99:12)
    at Api.addPlugin (/Users/theo/Projects/mobile/platforms/android/cordova/Api.js:223:57)
    at handleInstall (/Users/theo/.nvm/versions/node/v8.1.2/lib/node_modules/cordova/node_modules/cordova-lib/src/plugman/install.js:594:10)
    at /Users/theo/.nvm/versions/node/v8.1.2/lib/node_modules/cordova/node_modules/cordova-lib/src/plugman/install.js:357:28
    at _fulfilled (/Users/theo/.nvm/versions/node/v8.1.2/lib/node_modules/cordova/node_modules/q/q.js:787:54)
    at self.promiseDispatch.done (/Users/theo/.nvm/versions/node/v8.1.2/lib/node_modules/cordova/node_modules/q/q.js:816:30)
```

这个问题的原因就是新版本的cordova 7.0.0及以上版本对于新的AndroidManifest.xml路径还没有更新，导致找不到AndroidManifest.xml，下面给出两个解决办法：

1. 使用原先的cordova

```shell
npm uninstall -g cordova
npm install -g cordova@6.5.0
```

2. 重新布置安卓项目

```shell
cordova platform rm android
cordova platform add android
```

3. Cordova官方给出的解决办法
修改`config.xml`文件，具体可以查看网址<a href='https://cordova.apache.org/announcements/2017/12/04/cordova-android-7.0.0.html' target='_blank'>https://cordova.apache.org/announcements/2017/12/04/cordova-android-7.0.0.html</a>

```xml
<!-- An existing config.xml -->
<edit-config file="AndroidManifest.xml" target="/manifest/application" mode="merge">

<!-- needs to change to -->
<edit-config file="app/src/main/AndroidManifest.xml" target="/manifest/application" mode="merge">

```