---
title: 函数
date: 2020-03-19 16:29:27
categories: 
- Nodejs
tags: 
- note
---

Node.js中函数的使用与Javascript类似，即一个函数可以作为另一个函数的参数。我们可以**先定义一个函数，然后传递**，也可以**在传递参数的地方直接定义**函数。
举例代码：

```javascript
function say(word) {
  console.log(word);
}

function execute(someFunction, value) {
  someFunction(value);
}

execute(say, "Hello");
```
在以上代码中，say 函数作为execute函数的第一个变量进行了传递。这里传递的不是 say 的返回值，而是 say **本身**。如此，say 就变成了execute 中的本地变量 someFunction ，execute可以通过调用 someFunction() （带括号的形式）来使用 say 函数。
当然，因为 say 有一个变量， execute 在调用 someFunction 时可以传递这样一个变量。

## 匿名函数

**不要**用“先定义，再传递”的思想，**直接**在另一个函数的括号中定义和传递这个函数：

```javascript
function execute(someFunction, value) {
  someFunction(value);
}

execute(function(word){ console.log(word) }, "Hello");
```

在 execute 接受第一个参数的地方直接定义了我们准备传递给 execute 的函数。因为不用给函数起名，所以就叫匿名函数。

## 函数传递是如何让HTTP服务器工作的

HTTP服务器：

```javascript
var http = require("http");

http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("Hello World");
  response.end();
}).listen(8888);
```
<--`response.writeHead`和`response.end`’是一对-->
>该方法在消息中只能被调用一次，且必须在 response.end() 被调用之前调用
>`response.writeHead`:发送一个响应头给请求。 状态码是一个三位数的 HTTP 状态码,第二个参数 statusMessage 是可选的状态描述,最后一个参数 headers 是响应头。

这里同样可以用匿名函数达到同样的目的：

```javascript
var http = require("http");

function onRequest(request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("Hello World");
  response.end();
}

http.createServer(onRequest).listen(8888);
```
本篇博客是参考[Node.js菜鸟教程](https://www.runoob.com/nodejs/nodejs-function.html)，详情可跳至[菜鸟教程](https://www.runoob.com/nodejs/nodejs-function.html)。
***
后续本人会对写过的博客进行更新
***

