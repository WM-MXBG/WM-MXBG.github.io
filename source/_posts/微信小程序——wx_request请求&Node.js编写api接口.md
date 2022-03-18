---
title: wx:request请求&Node.js编写api接口
date: 2020-03-21 09:08:36
categories: 
- WeChat Applet
tags: 
- solution
- nodejs
---

## 简析wx.request

### url

```javascript
url:'开发者服务器接口地址'
```

接口地址填写你小程**绑定的合法域名**就可以，本地测试的话文章底部番外会提到。
***
### data
我们对url进行https请求跟在浏览器打开一个网址是一个道理。在浏览器打开网址，实际上是向这个域包所在的服务器发送了一个https请求。
格式为：

```javascript
 url:'http://xxxxxxx.com',   //这个接口随便写的
  data: {
     id: '1' ,
     dateTime: '2020-3-20'
  }
```
或者

```javascript
url:'http://xxxxxxx.com?id=1&dataTime=2020-3-20',
```
两者的代码意义一样， data里的值就是使用wx.request时url里面的参数，即问号后面的参数。
* data 数据说明：最终发送给服务器的数据是 String 类型，如果传入的 data 不是 String 类型，会被转换成 String 。具体说明小程序的[官方文档](https://developers.weixin.qq.com/miniprogram/dev/api/network/request/wx.request.html)里有，我就不复制粘贴了。
* ***
### header
https请求的头部header是在用户打开小程序后请求url时一起传到url所在的服务器时的头部信息，也就是说我们请求一个网址，实际上一起跟着这个网址传递到服务器的还有“请求头”；当请求完信息后，服务器再通过http协议把请求的信息返回给请求的用户，此时有一个“响应头”返回给用户，只不过两个头用户都感觉不到看不到罢了。
格式为：

```javascript
 header: {
      'content-type': 'application/json'
  },
```
* 注意一点：header 中不能设置 Referer，`content-type` 默认为 `application/json`。所以这个请求头的数据写上也没用……
***
### success
当一个https请求成功时，小程序就会自动触发这个返回成功信息的函数。  这个函数获取的是从开发者服务器返回的数据（一般为json格式），因为，获取到的数据就是我们要**渲染到小程序页面的数据**。（写到这里终于切题了……）
示例：

```javascript
//index.wxml
<view class="container">
  <block wx:for="{{contents}}">
    <view>
      <text>{{item.id}}---{{item.behavior}}---{{item.dateTime}}</text>
    </view>
  </block>
</view>
```

```javascript
//index.js
Page({
  data: {
    contents: {}
  },
  onLoad: function (options) {
    var that = this
    wx.request({
      url: 'https://xxxxxxx.com',
      data: {},
      method: 'GET',
      success: function (res) {
        // success
        that.setData({
          contents: res.data
        })
        console.log('require success', res)
      },
      fail: function (err) {
        // fail
        console.log('require fail' ,err)
      },
      complete: function () {
        // complete
      }
    })
  }
})
```
请求成功后获取到的数据就是success函数的参数res,打印这个数据如果如下所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321081223532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center=30x30@[TOC])
***
### fail&complete
 fail函数：如果接口调用失败的话，那么，小程序就会触发这个函数，返回错误信息。

  complete函数：接口调用结束的回调函数（调用成功、失败都会执行）。

  代码使用我在success中举例的代码即可，错误时就会打印错误信息。
***
  ### url的node.js代码
  现在就差node.js写的url接口代码了。
```javascript
//upLoad.js
var express = require('express')
var app = express()
var comments = [{
        id: '1',
        behavior: '起床',
        dateTime: '7:00'
    },
    {
        id: '2',
        behavior: '学习',
        dateTime: '7:30'
    },
    {
        id: '3',
        behaviore: '吃饭',
        dateTime: '9:00'
    },
    {
        id: '4',
        behavior: '午睡',
        dateTime: '13:00'
    }
]

app.get('/', function(req, res) {
    res.json(comments)
})
var server = app.listen(36631, function() {

    var host = server.address().address
    var port = server.address().port

    console.log("应用实例，访问地址为 http://%s:%s", host, port)

})
```
upLoad.js部分的代码我使用了express框架，当然不用也可以，我把不用express框架的代码也发一下：

```javascript
const http = require('http')
const url = require('url')
var comments = [{
        id: '1',
        behavior: '起床',
        dateTime: '7:00'
    },
    {
        id: '2',
        behavior: '学习',
        dateTime: '7:30'
    },
    {
        id: '3',
        behaviore: '吃饭',
        dateTime: '9:00'
    },
    {
        id: '4',
        behavior: '午睡',
        dateTime: '13:00'
    }
]

var jjson = JSON.stringify(comments)
http.createServer(function(req, res) {
    res.setHeader('Content-Type', 'text/palin; charset=utf-8');
    var pathname = url.parse(req.url).pathname
    console.log(pathname)
    if (pathname == '/') {
        res.end(jjson)
    }
}).listen(36631)
console.log("http://127.0.0.1:36631")
```
如果不用框架的话可能会导致浏览器浏览时出现中文乱码，只要写入`res.setHeader('Content-Type', 'text/palin; charset=utf-8');`即可，要用`res.writeHead`需要和`res.end`组合，具体怎么用还是自行搜索吧，我也不太了解。
upLoad.js的代码我都设置了路由，不用路由的话把

```javascript
app.get('/', function(req, res) {
    res.json(comments)
})
var server = app.listen(36631, function() {

    var host = server.address().address
    var port = server.address().port

    console.log("应用实例，访问地址为 http://%s:%s", host, port)

})
```
这部分换成这
```javascript
app.listen(36631)
app.get('/',function(req,res) {
    res.json(comments)
})
console.log("http://127.0.0.1:36631")
```
部分就行。不用express框架的不用路由我没写。
***
代码就三部分，upLoad.js代码写在项目根目录下就行，你要是把它和.wxml文件放在一个目录里也行。这里有一个比较有趣的点，就是无论你在项目根目录还是带.wxml的目录里放upLoad.js，小程序成功编译后在你没放的目录里就会自动生成一个upLoad.js，只不过新生成的内容为空。
在upLoad.js的目录下果断cmd运行node，首次运行就在你写upLoad.js的目录下运行，运行成功后再浏览器输入访问地址`http://127.0.0.1:36631`就能看到“id”，“dateTime”这些数据了，之后在开发者工具里点击编译就能看到结果了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321082735880.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

## 这就完事了~
***
番外再说几点：

 1. 开发者工具编译成功后虽然会有两个upLoad.js，但是再使用node运行upLoad.js文件时还是要在有内容的upLoad.js的目录下运行，否则就会出现下面这种情况（我写博客的时候用的名称是upLoad.js，我个人用的名称是upLoadUrl.js）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321083627116.jpg#pic_center)
运行命令输入完啥也没有，正常应该是这样
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321083803415.jpg#pic_center)
然后拿着这个地址去浏览器就能打开
 2. 本人也是在本地调试，用的Charles这个软件将https域名转为本地接口，具体用法可以去“一个人也很酷”的csdn博客下找一篇名为《微信小程序 本地开发》的博客。本菜鸟最近才写博客，不知道引用别人博客链接会不会犯法……，所以就这样写了，哈哈。我想说的是这个Charles过一会不用就自动停止了，这时候开发者工具编译就会出错
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321085449791.jpg#pic_center)
    再启动一次Charles就好了。本地调试url处写的接口地址写你小程序绑定的域名就行，和文章开头一样。
***
 大致写这么多应该就够了，如果有错请各位道友指出，本人一定及时修改。也欢迎各位在评论区讨论交流，我也是菜鸟一枚，咱们可以共同进步↖(^ω^)↗
 （本人菜鸡一枚,所以只是简析一下wx:request的功能用法，本人也是看了许多别的前辈的文章加自己的理解写的这篇博客，写的不好见谅，小声bb~）
***
 后续本人会对写过的博客进行更新

***
