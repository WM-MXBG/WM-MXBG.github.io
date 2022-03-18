---
title: GET/POST请求
date: 2020-03-19 18:37:12
categories: 
- Nodejs
tags: 
- note
---



表单提交到服务器一般都使用 GET/POST 请求。
## 获取GET请求内容
由于GET请求直接被嵌入在路径中，URL是完整的请求路径，包括了?后面的部分，因此你可以手动解析后面的内容作为GET请求的参数。
* node.js 中 url 模块中的 parse 函数提供了这个功能。

```javascript
var http = require('http');
var url = require('url');
var util = require('util');
 
http.createServer(function(req, res){
    res.writeHead(200, {'Content-Type': 'text/plain; charset=utf-8'});
    res.end(util.inspect(url.parse(req.url, true)));
}).listen(3000);
```
在浏览器中访问 **http://localhost:3000/user?name=菜鸟教程&url=www.runoob.com** 然后查看返回结果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319165130303.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
## 获取 URL 的参数
我们可以使用 url.parse 方法来解析 URL 中的参数，代码如下：

```javascript
var http = require('http');
var url = require('url');
var util = require('util');
 
http.createServer(function(req, res){
    res.writeHead(200, {'Content-Type': 'text/plain'});
 
    // 解析 url 参数
    var params = url.parse(req.url, true).query;
    res.write("网站名：" + params.name);
    res.write("\n");
    res.write("网站 URL：" + params.url);
    res.end();
 
}).listen(3000);
```
在浏览器中访问 **http://localhost:3000/user?name=菜鸟教程&url=www.runoob.com** 然后查看返回结果:
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031916531559.jpg)
第二次访问可能会出现乱码，在第二部分代码中`res.writeHead`语句改写为`(200, {'Content-Type': 'text/plain'}); `即可。乱码问题出现有时候都不知道自己到底漏了啥，好在也不是啥大问题
注意看第一次访问 **http://localhost:3000/user?name=菜鸟教程&url=www.runoob.com** 的结果中*query*的值，在使用 url.parse 方法来解析 URL 中的参数时，对象就是它。
## 获取 POST 请求内容
POST 请求的内容全部的都在请求体中，http.ServerRequest 并没有一个属性内容为请求体，因为等待请求体和解析请求体费时间。

```javascript
//基本语法结构说明
var http = require('http');
var querystring = require('querystring');
var util = require('util');
 
http.createServer(function(req, res){
    // 定义了一个post变量，用于暂存请求体的信息
    var post = '';     
 
    // 通过req的data事件监听函数，每当接受到请求体的数据，就累加到post变量中
    req.on('data', function(chunk){    
        post += chunk;
    });
 
    // 在end事件触发后，通过querystring.parse将post解析为真正的POST请求格式，然后向客户端返回。
    req.on('end', function(){    
        post = querystring.parse(post);
        res.end(util.inspect(post));
    });
}).listen(3000);
```
以下实例表单通过 POST 提交并输出数据：

```javascript
var http = require('http');
var querystring = require('querystring');
 
var postHTML = 
  '<html><head><meta charset="utf-8"><title>菜鸟教程 Node.js 实例</title></head>' +
  '<body>' +
  '<form method="post">' +
  '网站名： <input name="name"><br>' +
  '网站 URL： <input name="url"><br>' +
  '<input type="submit">' +
  '</form>' +
  '</body></html>';
 
http.createServer(function (req, res) {
  var body = "";
  req.on('data', function (chunk) {
    body += chunk;
  });
  req.on('end', function () {
    // 解析参数
    body = querystring.parse(body);
    // 设置响应头部信息及编码
    res.writeHead(200, {'Content-Type': 'text/html; charset=utf8'});
 
    if(body.name && body.url) { // 输出提交的数据
        res.write("网站名：" + body.name);
        res.write("<br>");
        res.write("网站 URL：" + body.url);
    } else {  // 输出表单
        res.write(postHTML);
    }
    res.end();
  });
}).listen(3000);
```
执行结果 Gif 演示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319170115910.gif)
因为if语句的原因，如果闭的网站名和URL没都输入，例如只输入了一行，页面就会刷新成最开始的样子。GIF图我就不放了，毕竟动手才能快乐嘛（绝不是因为我懒）

本篇博客是参考[Node.js菜鸟教程]https://www.runoob.com/nodejs/node-js-get-post.html)，详情可跳至[菜鸟教程](https://www.runoob.com/nodejs/node-js-get-post.html)。
***
后续本人会对写过的博客进行更新
***

