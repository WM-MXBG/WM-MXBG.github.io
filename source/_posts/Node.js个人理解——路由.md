---
title: 路由
date: 2020-03-19 18:36:54
categories: 
- Nodejs
tags: 
- note
---

我们要为路由提供请求的 URL 和其他需要的 GET 及 POST 参数，随后路由需要根据这些数据来执行相应的代码。

因此，我们需要查看 HTTP 请求，从中提取出请求的 URL 以及 GET/POST 参数。这一功能暂定为我们的HTTP服务器的功能。

我们需要的所有数据都会包含在 request 对象中，该对象作为 onRequest() 回调函数的第一个参数传递。但是为了解析这些数据，我们需要额外的 Node.JS 模块，它们分别是 url 和 querystring 模块。

```javascript
                   url.parse(string).query
                                           |
           url.parse(string).pathname      |
                       |                   |
                       |                   |
                     ------ -------------------
http://localhost:8888/start?foo=bar&hello=world
                                ---       -----
                                 |          |
                                 |          |
              querystring.parse(queryString)["foo"]    |
                                            |
                         querystring.parse(queryString)["hello"
```
也可以用 querystring 模块来解析 POST 请求体中的参数，稍后会有演示。
现在我们来给 onRequest() 函数加上一些逻辑，用来找出浏览器请求的 URL 路径：

```javascript
//server.js
var http = require("http");
var url = require("url");
 
function start() {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }
 
  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}
 
exports.start = start;
```
应用现在可以通过请求的 URL 路径来区别不同请求了--这使我们得以使用路由（还未完成）来将请求以 URL 路径为基准映射到处理程序上。
在我们所要构建的应用中，这意味着来自 /start 和 /upload 的请求可以使用不同的代码来处理。稍后我们将看到这些内容是如何整合到一起的。

现在我们可以来编写路由了，建立一个名为 router.js 的文件，添加以下内容：

```javascript
//router.js
function route(pathname) {
  console.log("About to route a request for " + pathname);
}
 
exports.route = route;
```
上面代码确实啥也没干……，不过现在重点是如何把路由和服务器整合起来。在这里不推荐通过硬编码的方式将这一依赖项绑定到服务器上，使用依赖注入的方式较松散地添加路由模块的方法更为广泛
首先，我们来扩展一下服务器的 start() 函数，以便将路由函数作为参数传递过去，server.js 文件代码如下：

```javascript
//server.js
var http = require("http");
var url = require("url");
 
function start(route) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");
 
    route(pathname);
 
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }
 
  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}
 
exports.start = start;
```
同时，我们会相应扩展 index.js，使得路由函数可以被注入到服务器中：

```javascript
// index.js
var server = require("./server");
var router = require("./router");
 
server.start(router.route);
```
* 此时传递的函数依旧啥也没干
如果现在启动应用（node index.js，始终记得这个命令行），随后请求一个URL，你将会看到应用输出相应的信息，这表明我们的HTTP服务器已经在使用路由模块了，并会将请求的路径传递给路由：

```javascript
$ node index.js
Server has started.
```
浏览器访问 http://127.0.0.1:8888/，输出结果如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319164641372.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
本篇博客是参考[Node.js菜鸟教程](https://www.runoob.com/nodejs/nodejs-router.html)，详情可跳至[菜鸟教程](https://www.runoob.com/nodejs/nodejs-router.html)。
***
后续本人会对写过的博客进行更新
***

