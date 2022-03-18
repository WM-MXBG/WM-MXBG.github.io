---
title: 调用摄像头拍照上传
date: 2020-04-15 14:54:59
categories: 
- WeChat Applet
tags: 
- solution
- nodejs
---

## 前端部分

```javascript
///.wxml
<view class="tui-picker-content">
<button bindtap="submitImage">提交照片</button>
<view>
```

```javascript
//js
submitImage:function(e){
  var that = this;
  var roomID = that.data.roomID;0
  wx.chooseImage({
    count: 1,
    sizeType: ['original'],
    sourceType: ['album', 'camera'],
    success: (res) => {
            const tempFilePaths = res.tempFilePaths[0]
            that.setData({
                imgSrc: tempFilePaths
            });
            wx.uploadFile({
                url: 'https://www.xxxxxx.com/img', //服务器接口地址
                filePath: tempFilePaths,
                name: 'file',
                formData:{
                  'roomID': roomID
                },
                header: {

                'content-type': 'multipart/form-data'

                },
                success: function (res) {
                  var data = res.data;
                  console.log(data);
                },
                fail: function (e) {
                  console.log(e.stack);
                }
            })
         }
    })
}
```
前端代码没啥好讲的其实……，如有疑问可以私信我或者评论区见哦
***
## 后端部分
后端部分才是重重之重

```javascript
var express = require("express");
var formidable = require("formidable");
var path = require("path")
var fs = require("fs")
var app = express();

app.post("/img", (req, res) => {
  var form = new formidable.IncomingForm();//既处理表单，又处理文件上传
  //设置文件上传文件夹/路径，__dirname是一个常量，为当前路径
  let uploadDir = path.join("../img/");
  form.uploadDir = uploadDir;// 设置文件的临时存储存储路径，如果不设置的话默认设置到 os.tmpdir()
  form.encoding = 'utf-8'; // 将请求信息编码设置为utf-8
  form.keepExtensions = true; // nodejs会默认将文件信息保存在一个没有后缀的文件中,设置为true将保留后缀
  form.maxFieldsSize = 2 * 1024 * 1024; //文件大小
  form.parse(req, (err, fields, files) => {
    var roomID = fields.roomID;
    let oldPath = files.file.path; //这里的路径是图片的本地路径
    let newPath = '/home/ubuntu/img/' + roomID + '.jpg';
    fs.rename(oldPath, newPath, function (err) {
      if (!err) {
        console.log(newPath);
      }
    })
    res.send({
      code: 200,
      msg: '/img/' + newPath
    });

  })
})



var server = app.listen(6666, function () {

  var host = server.address().address
  var port = server.address().port

  console.log("应用实例，访问地址为 http://%s:%s", host, port)
})
```
这个图片上传我一开始以为很简单（确实不难），但是后来却出了问题，原因就是对node.js的不熟悉，尤其是formidable这个东东。我一开始也是在网上找的后端代码，里面内容几乎没有，所以自然就失败了……。后来我又找到了一次代码，其实当时找的代码再加上我自己改编后已经满足条件了，但是在路径设置上出了问题，如果当时研究研究formidable就好了，我太过依赖网上找的代码。后来我的队长找了一篇[文章](https://cloud.tencent.com/developer/article/1467373)，并且给我之前就把路径问题解决了，当我看到代码和我差不多的时候是哭笑不得。自己就差一点就成功了，所以仔细学语言真的很重要，对这个模块多了解才能发现细微的错误。其实到现在我还没有深入了解formidable，但是代码我差不多都看懂了。注释的部分其实都好理解，但是我们在解决传值的问题的时候就需要注意了。
**fields** 和 **files**
注意看node代码中路径的获取，是在files里获取的。
小程序前端中formdata中传的值roomID是在fields里获取的。
在解决路径问题后我在网上搜索过node怎么接受formdata的传值，但是网上关于小程序的formdata的传值记录不多。我在微信开发者社区里找到了一点信息，有说让用插件的，不过我看都挺复杂。而且在评论中有人提到小程序的formdata是不严格的，反正是有点毛病。后来我通过获取路径的方法寻找formdata传值，多次打印console.log，终于在fields里找到了roomID。至于怎么利用传过来的值我就不细说了。
其实在文章末尾的链接文章中有保持图片原有后缀的代码，但是那个的前提是要先获取后缀，是 files.file.type，这个获取的即使图片后缀。


图片换名：

```javascript
fs.rename(oldPath, newPath, function (err) {
      if (!err) {
        console.log(newPath);
      }
    })
```

还有最后特别重要的一点——**路径**
注意看图片新名称newPath的拼接方式 
  `let newPath = '/home/ubuntu/img/' + roomID + '.jpg';`

最终存储在服务器的图片名称显示为 roomID.jpg，

roomID的值是101，那图片显示的名字就是101.jpg，

roomID前面的一串路径就是图片在服务器的位置。

大家可以看我的另一篇[博客](https://editor.csdn.net/md/?articleId=105526742)，里面的WinSCP部分的截图就有显示我图片储存位置的路径。


参考文章：
[https://www.cnblogs.com/JobsOfferings/p/JonsOfferings_node_imgUp.html#4549239](https://www.cnblogs.com/JobsOfferings/p/JonsOfferings_node_imgUp.html#4549239)

[https://www.cnblogs.com/xiaofeixiang/p/5140673.html](https://www.cnblogs.com/xiaofeixiang/p/5140673.html)
