---
title: "IONIC跨域问题"
date: 2018-07-22T14:21:50+08:00
tags: ["移动开发","IONIC","跨域"]
categories: ["移动开发","IONIC","跨域"]
toc: false
---

![https://oss.lucoder.com/uploads/2018/07/22/20180726191011.png](https://oss.lucoder.com/uploads/2018/07/22/20180726191011.png)

Ionic跨域问题是一个重要的问题，但是一定要注意一点真机里网页的访问走的是file://协议，而非http://或https://，因此不会出现跨域问题：`ionic跨域问题只存在于调试`（包括浏览器调试和--livereload，真机的livereload仍然是基于浏览器，还会报错），如果真机发布不会出现任何问题，下面给出几个解决方法：

## 设置代理模式

现在假设有一个服务器：`https://api.lucoder.com/services/jpush/`，解决这个其实很简单，后端服务器不需要做任何处理，只需要在ionic的配置文件（ionic.config.json）里加上proxy这个字段，
设置ionic代理，如下所示：
```json
{
  "name": "proxy-example",
  "app_id": "",
  "proxies": [
    {
      "path": "/services/",//这相当于https://api.lucoder.com/services/
      "proxyUrl": "https://api.lucoder.com/services/" //这部分蓝色就是之前的原api请求地址
    }
  ]
}
```
然后调用的时候可以直接使用下面代码：
```typescript
url:string = "jpush/";
push():void{
  return new Promise((resolve, reject)=>{
    this.http.get(this.url + "push").subscribe(res => {
      resolve(res.json)
    }, err => {
      reject(err);
    });
  });
}
```
浏览器调试完全没问题，使用`livereload`也没有问题，但是`真题运行需要修改掉`，复原即可，否则就会出现错误，另外的这个`proxies`是一个数组，可以有多个代理！

## 设置浏览器安全项目

这个方法是最简单的，最适合chrome浏览器调试的，只需要将`--disable-web-security`放到快捷方式里面即可。


## 不成熟的设置后台代码

为什么说这个是不成熟的，一是好多时候我们需要使用的是真机调试，二是很多时候我们会参考三方的api，我们不可能去修改别人的主机，和后台代码。下面介绍两种方法：

#### nginx

【转载】:[https://www.cnblogs.com/gabrielchen/p/5066120.html](https://www.cnblogs.com/gabrielchen/p/5066120.html)

上面已经说到，禁止跨域问题其实是浏览器的一种安全行为，而现在的大多数解决方案都是用标签可以跨域访问的这个漏洞或者是技巧去完成，但都少不了目标服务器做相应的改变，而我最近遇到了一个需求是，目标服务器不能给予我一个header，更不可以改变代码返回个script，所以前5种方案都被我否决掉。最后因为我的网站是我自己的主机，所以我决定搭建一个nginx并把相应代码部署在它的下面，由页面请求本域名的一个地址，转由nginx代理处理后返回结果给页面，而且这一切都是同步的。
　　关于nginx的一些基本配置和安装请看我的另一篇博客，下面直接讲解如何配置一个反向代理。
　　首先找到nginx.conf或者nginx.conf.default 或者是default里面的这部份   
 　其中server代表启动的一个服务，location 是一个定位规则。
```config
location /｛   #所有以/开头的地址，实际上是所有请求
root  html     ＃去请求../html文件夹里的文件,其中..的路径在nginx里面有定义，安装的时候会有默认路径，详见另一篇博客
index  index.html index.htm  ＃首页响应地址
｝
```
　　从上面可以看出location是nginx用来路由的入口，所以我们接下来要在location里面完成我们的反向代理。

　　假如我们我们是www.a.com/html/msg.html 想请求www.b.com/api/?method=1&para=2；

　　我们的ajax：

var url = 'http://www.b.com/api/msg?method=1&para=2'；

```js
$.ajax({
type: "GET",
url:url,
success: function(res){..},
....
})
```
　　上面的请求必然会遇到跨域问题，这时我们需要修改一下我们的请求url，让请求发在nginx的一个url下。
```js
var url = 'http://www.b.com/api/msg?method=1&para=2'； 
var proxyurl ＝ 'msg?method=1&para=2'；
//假如实际地址是 www.c.com/proxy/html/api/msg?method=1&para=2; www.c.com是nginx主机地址
 $.ajax({ 
type: "GET", 
url:proxyurl, 
success: function(res){..}, 
.... 
})
```
　　再在刚才的路径中匹配到这个请求，我们在location下面再添加一个location。
```conf
location ^~/proxy/html/{
rewrite ^/proxy/html/(.*)$ /$1 break;
proxy_pass http://www.b.com/;
}
```
以下做一个解释：
1. '^~ /proxy/html/ '

　　就像上面说的一样是一个匹配规则，用于拦截请求，匹配任何以 /proxy/html/开头的地址，匹配符合以后，停止往下搜索正则。

2. rewrite ^/proxy/html/(.*)$ /$1 break;

　　代表重写拦截进来的请求，并且只能对域名后边的除去传递的参数外的字符串起作用，例如www.c.com/proxy/html/api/msg?method=1&para=2重写。只对/proxy/html/api/msg重写。

　　rewrite后面的参数是一个简单的正则 ^/proxy/html/(.*)$ ,$1代表正则中的第一个(),$2代表第二个()的值,以此类推。
　　break代表匹配一个之后停止匹配。

3. proxy_pass

　　既是把请求代理到其他主机，其中 http://www.b.com/ 写法和 http://www.b.com写法的区别如下:
不带/

```config
 location /html/
{
　　proxy_pass http://b.com:8300;  
}
```
带/
```config
location /html/  
{  
    proxy_pass http://b.com:8300/;  
} 
```
上面两种配置，区别只在于proxy_pass转发的路径后是否带 “/”。
　　
针对情况1，如果访问url = http://server/html/test.jsp，则被nginx代理后，请求路径会便问http://proxy_pass/html/test.jsp，将test/ 作为根路径，请求test/路径下的资源。
　　
针对情况2，如果访问url = http://server/html/test.jsp，则被nginx代理后，请求路径会变为 http://proxy_pass/test.jsp，直接访问server的根资源。

修改配置后重启nginx代理就成功了。

#### 后代代码

【转载】:[https://blog.csdn.net/qq_22172905/article/details/52934055](https://blog.csdn.net/qq_22172905/article/details/52934055)

修改`web.config`文件：
```xml
<system.webServer>  
     <httpProtocol> 
       <customHeaders> 
      <add name="Access-Control-Allow-Methods" value="OPTIONS,POST,GET"/> 
      <add name="Access-Control-Allow-Headers" value="x-requested-with"/> 
      <add name="Access-Control-Allow-Origin" value="*" /> 
    </customHeaders> 
     </httpProtocol> 
     </system.webServer>
```

注：在web.config里添加上面是代码，那你的这个web项目都被允许为跨域访问了。如果你不想全部都设置为允许跨域访问，只想允许某一个一般处理文件，那你可以采用下面的代码

```cs
context.Response.ClearHeaders(); 
context.Response.AppendHeader("Access-Control-Allow-Origin","*"); 
string requestHeaders = context.Request.Headers["Access-Control-Request-Headers"]; 
context.Response.AppendHeader("Access-Control-Allow-Headers", 
string.IsNullOrEmpty(requestHeaders) ? "*" : requestHeaders); 
context.Response.AppendHeader("Access-Control-Allow-Methods", "POST, OPTIONS")；
```

【参考】:[https://blog.ionicframework.com/handling-cors-issues-in-ionic/](https://blog.ionicframework.com/handling-cors-issues-in-ionic/)