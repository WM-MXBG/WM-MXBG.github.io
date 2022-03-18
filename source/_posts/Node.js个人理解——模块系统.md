---
title: 模块系统
date: 2020-03-19 16:28:42
categories: 
- Nodejs
tags: 
- note
---

## 创建模块

Node.js给了两个对象
 - exports	——模块公开的接口
 - require 	—— 从外部获取模块的接口（获取模块的exports对象）

实例：
&emsp;创建hello.js文件，代码如下：
```javascript
exports.world = function() {
  console.log('Hello World');
}
```
&emsp;创建main.js文件，代码如下：

```javascript
var hello = require('./hello');
hello.world();
```
hello.js 通过 exports 对象把 world 作为模块的访问接口，在 main.js 中通过 require('./hello') 加载这个模块，然后就可以直接访 问 hello.js 中 exports 对象的成员函数了。

### 对象封装到模块

格式如下：

```javascript
module.exports = function() {
  // ...
}
```
&emsp;实例：

```javascript
//hello.js 
function Hello() { 
    var name; 
    this.setName = function(thyName) { 
        name = thyName; 
    }; 
    this.sayHello = function() { 
        console.log('Hello ' + name); 
    }; 
}; 
module.exports = Hello;
```
这样就可以直接获得这个对象了：

```javascript
//main.js 
var Hello = require('./hello'); 
hello = new Hello(); 
hello.setName('BYVoid'); 
hello.sayHello(); 
```
封装到模块中后可以直接引用，和封装函数前的代码案例一样，封装函数的代码案例中加了一些更改，但无关引用。两者的本质变化就是接口的变化。使用 module.exports = Hello 代替了exports.world = function(){}。 在外部引用该模块时，其接口对象就是要输出的 Hello 对象本身，而不是原先的 exports。

>exports 和 module.exports 的使用
如果要对外暴露属性或方法，就用 exports 就行，要暴露对象(类似class，包含了很多属性和方法)，就用 module.exports
>>不建议同时使用 exports 和 module.exports。
如果先使用 exports 对外暴露属性或方法，再使用 module.exports 暴露对象，会使得 exports 上暴露的属性或者方法失效。
原因在于，exports 仅仅是 module.exports 的一个引用
## 服务端模块

Node.js 中自带了一个叫做 http 的模块，我们在我们的代码中请求它并把返回值赋给一个本地变量。这把我们的本地变量变成了一个拥有所有 http 模块所提供的公共方法的对象。
http模块：

```javascript
var http = require("http");

...

http.createServer(...);
```
Node.js 的 require 方法中的文件查找策略：
Node.js 中存在 4 类模块（原生模块和3种文件模块），尽管 require 方法极其简单，但是内部的加载却是十分复杂的，其加载优先级也各自不同。

### 从文件模块缓存中加载
尽管原生模块与文件模块的优先级不同，但是都会优先从文件模块的**缓存**中加载已经存在的模块。

### 从原生模块加载
原生模块的优先级仅次于文件模块缓存的优先级。require 方法在解析文件名之后，优先检查模块是否在原生模块列表中。以http模块为例，尽管在目录下存在一个 http/http.js/http.node/http.json 文件，require("http") 都不会从这些文件中加载，而是从原生模块中加载。

原生模块也有一个缓存区，同样也是优先从缓存区加载。如果缓存区没有被加载过，则调用原生模块的加载方式进行加载和执行。

### 从文件加载
当文件模块缓存中不存在，而且不是原生模块的时候，Node.js 会解析 require 方法传入的参数，并从文件系统中加载实际的文件。

总结：
加载顺序：**文件模块缓存>原生模块缓存>原生模块>文件模块**
require方法接受以下几种参数的传递:

 - http、fs、path等，原生模块
 - ./mod或../mod，相对路径的文件模块
 - /pathtomodule/mod，绝对路径的文件模块
 - mod，非原生模块的文件模块

本篇博客是参考[Node.js菜鸟教程](https://www.runoob.com/nodejs/nodejs-module-system.html)，其中部分内容本人还没理解，不想单纯的复制粘贴就没有引入，详情可跳至[菜鸟教程](https://www.runoob.com/nodejs/nodejs-module-system.html)。
***
后续本人会对写过的博客进行更新
***

