---
title: Express框架安装
date: 2020-03-19 18:37:27
categories: 
- Nodejs
tags: 
- note
---

## Express 简介

Express 框架核心特性：

 - 可以设置中间件来响应 HTTP 请求
 - 定义了路由表用于执行不同的 HTTP 请求动作。
 - 可以通过向模板传递参数来动态渲染 HTML 页面。
***
## 安装 Express
安装 Express 并将其保存到依赖列表中：

```javascript
$ cnpm install express --save
```
或者
```javascript
$ npm install express --save
```
 以上命令会将 Express 框架安装在当前目录的 node_modules 目录中， node_modules 目录下会自动创建 express 目录。以下几个重要的模块是需要与 express 框架一起安装的：


 - body-parser - node.js 中间件，用于处理 JSON, Raw, Text 和 URL 编码的数据。
 - cookie-parser - 这就是一个解析Cookie的工具。通过req.cookies可以取到传过来的cookie，并把它们转成对象。
 - multer - node.js 中间件，用于处理 enctype="multipart/form-data"（设置表单的MIME编码）的表单数据。


```javascript
$ cnpm install body-parser --save
$ cnpm install cookie-parser --save
$ cnpm install multer --save
```
或者

```javascript
$ npm install body-parser --save
$ npm install cookie-parser --save
$ npm install multer --save 
```
安装完后，我们可以查看下 express 使用的版本号：

```javascript
$ cnpm list express
/data/www/node
└── express@4.15.2  -> /Users/tianqixin/www/node/node_modules/.4.15.2@express
```
windows的自己去掉"$"，这个应该都会吧……
***
npm下载慢都知道，可以更换淘宝镜像源：

```javascript
npm config set registry https://registry.npm.taobao.org 
```
– 配置后可通过下面方式来验证是否成功 
```javascript
npm config get registry 
– 或npm info express
```
题外话，关于Node.js环境配置和Express安装刚详细的教程推荐一篇[知乎文章](https://zhuanlan.zhihu.com/p/77594251)
***
## 第一个 Express 框架实例
接下来我们使用 Express 框架来输出 "Hello World"。
以下实例中我们引入了 express 模块，并在客户端发起请求后，响应 "Hello World" 字符串。
创建 express_demo.js 文件，代码如下所示：

```javascript
//express_demo.js 文件
var express = require('express');
var app = express();
 
app.get('/', function (req, res) {
   res.send('Hello World');
})
 
var server = app.listen(8081, function () {
 
  var host = server.address().address
  var port = server.address().port
 
  console.log("应用实例，访问地址为 http://%s:%s", host, port)
 
})
```
执行以上代码：

```javascript
$ node express_demo.js 
应用实例，访问地址为 http://0.0.0.0:8081
```
在浏览器中访问 http://127.0.0.1:8081，结果如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319173423380.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
### 请求和响应
(复制粘贴于[菜鸟教程](https://www.runoob.com/nodejs/nodejs-express-framework.html)）
Express 应用使用回调函数的参数： **request** 和 **response** 对象来处理请求和响应的数据。

```javascript
app.get('/', function (req, res) {
   // --
})
```
**request** 和 **response** 对象的具体介绍：

**Request 对象** - request 对象表示 HTTP 请求，包含了请求查询字符串，参数，内容，HTTP 头部等属性。常见属性有：

 1. req.app：当callback为外部文件时，用req.app访问express的实例
 2. req.baseUrl：获取路由当前安装的URL路径
 3. req.body / req.cookies：获得「请求主体」/ Cookies
 4. req.fresh / req.stale：判断请求是否还「新鲜」
 5. req.hostname / req.ip：获取主机名和IP地址
 6. req.originalUrl：获取原始请求URL
 7. req.params：获取路由的parameters
 8. req.path：获取请求路径
 9. req.protocol：获取协议类型
 10. req.query：获取URL的查询参数串
 11. req.route：获取当前匹配的路由
 12. req.subdomains：获取子域名
 13. req.accepts()：检查可接受的请求的文档类型
 14. req.acceptsCharsets / req.acceptsEncodings / req.acceptsLanguages：返回指定字符集的第一个可接受字符编码
 15. req.get()：获取指定的HTTP请求头
 16. req.is()：判断请求头Content-Type的MIME类型

**Response 对象** - response 对象表示 HTTP 响应，即在接收到请求时向客户端发送的 HTTP 响应数据。常见属性有：
 1. res.app：同req.app一样
 2. res.append()：追加指定HTTP头
 3. res.set()在res.append()后将重置之前设置的头
 4. res.cookie(name，value [，option])：设置Cookie
 5. opition: domain / expires / httpOnly / maxAge / path / secure / signed
 6. res.clearCookie()：清除Cookie
 7. res.download()：传送指定路径的文件
 8. res.get()：返回指定的HTTP头
 9. res.json()：传送JSON响应
 10. res.jsonp()：传送JSONP响应
 11. res.location()：只设置响应的Location HTTP头，不设置状态码或者close response
 12. res.redirect()：设置响应的Location HTTP头，并且设置状态码302
 13. res.render(view,[locals],callback)：渲染一个view，同时向callback传递渲染后的字符串，如果在渲染过程中有错误发生next(err)将会被自动调用。callback将会被传入一个可能发生的错误以及渲染后的页面，这样就不会自动输出了。
 14. res.send()：传送HTTP响应
 15. res.sendFile(path [，options] [，fn])：传送指定路径的文件 -会自动根据文件extension设定Content-Type
 16. res.set()：设置HTTP头，传入object可以一次设置多个头
 17. res.status()：设置HTTP状态码
 18. res.type()：设置Content-Type的MIME类型
***
## 路由
我们已经了解了 HTTP 请求的基本应用，而路由决定了由谁(指定脚本)去响应客户端请求。
在HTTP请求中，我们可以通过路由提取出请求的URL以及GET/POST参数。
接下来我们扩展 Hello World，添加一些功能来处理更多类型的 HTTP 请求。
创建 express_demo2.js 文件，代码如下所示：
**啥也没有**
在原[菜鸟教程](https://www.runoob.com/nodejs/nodejs-express-framework.html)文档中剩下的就是举例，可以自己敲代码来验证，本篇博客是笔记向，就不过多复制粘贴了。
当然，在对代码更加深入了解后会回来更新的。


本篇博客是参考[Node.js菜鸟教程](https://www.runoob.com/nodejs/nodejs-express-framework.html)，详情可跳至[菜鸟教程](https://www.runoob.com/nodejs/nodejs-express-framework.html)。
***
后续本人会对写过的博客进行更新

***
