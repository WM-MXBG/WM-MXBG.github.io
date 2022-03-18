---
title: 获取openid
date: 2020-04-15 13:33:13
categories: 
- WeChat Applet
tags: 
- solution
- nodejs
---

## 前言

说实话，获取OpenID这部分操作是我做小程序到现在耗时最长、精力投入最多的！所以我打算来个比较详细的讲解。因为这个是上周完成的操作，后续我去完善其他功能了，所以当时的报错截图我并没有保留多少，而且有一部分内容现在还有点忘了…………QAQ。	
综上，一定要养成随笔记录的好习惯!
***

## OpenID和unionID
OpenID是公众号的普通用户的一个唯一的标识，只针对当前的应用有效。公众号和小程序都是应用，所以不同公众号和小程序对应的openid是不同的，但是单拿出来一个应用的openid就是不变的。
其实获取身份唯一标识最好是unionID，这个不管在多少个应用里值永远唯一，在多个应用中确定用户身份用unionID无疑是最好的，但是获取unionID的首要条件是需要绑定微信开放平台。![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415081438498.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
当时我绑定半天微信开放平台都不成功，可能和我小程序账号未认证有关吧，后来了解到绑定开放平台后需要花钱认证（不是小程序账号认证）才能获取，个人感觉麻烦，而且我们的小程序也只有一个，综合考虑用OpenID就足够了。
获取unionIDD的官方文档比较详细，地址：[https://developers.weixin.qq.com/doc/offiaccount/User_Management/Get_users_basic_information_UnionID.html#UinonId](https://developers.weixin.qq.com/doc/offiaccount/User_Management/Get_users_basic_information_UnionID.html#UinonId)

***


## 配置问题
这个配置问题是在我解决过程中最无奈也是最关键的部分，作为一个程序猿，不能只会前后端语言，一些网站配置啊什么的也得会，后面我会说不会是多么的痛苦！

 1. 首先就要在微信公众平台配置合法域名，这个就不用我多说了，域名备案，给域名安装SSL证书这些操作我就不教学了。只有配置合法域名之后才能做以后的步骤。
 2. 设置端口号，小程序默认端口号是443，在小程序前端js部分请求的url直接填 https://"你的域名” 就行，不用再后面加端口号。我觉得给域名多设置几个端口号是有必要的，因为在你本地调试的时候很有可能会出现端口被占用的情况，这时候换个之前设过的端口号就可。.[^1]
 3. 既然都涉及服务器了，就需要把后端代码上传给服务器，然后在服务器上运行你的后端文件。
 ①：本地与远程计算机间安全的复制文件（本地——服务器）的软件推荐——WinSCP
    在刚进WinSCP页面会有这个界面![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415094634411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    主机名：服务器ip
    用户名：服务器账号
    密码：服务器密码
    在左面右键文件上传，上传文件夹或文件都行。![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415095241747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415095257760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    ②：安全终端模拟软件推荐——Xsell
    Xshell可以在Windows界面下用来访问远端不同系统下的服务器，从而比较好的达到远程控制终端的目的。就是说在windows下进行linux操作命令。首先新建会话
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041510005655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    第三步：填写服务器账号![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415112355283.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    第四部：填写服务器密码![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415112402601.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    第五步： 执行node命令，后端js文件在哪里就cd到哪里执行
    当然，在这之前得先在服务器上配置好node.js环境，像express这些可以在下面那个界面npm安装（也可以执行node命令）![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415100720965.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
    因为我是在小队里，所以有些东西不是我去干。像服务器配置环境这些应该是我的活，但是我对服务器这一块了解不多，包括在服务器上运行后端的js文件也是要获取openid才开始这么干的。而且我在队伍里进度算慢的，所以我的队长帮助我整了不少活，包括这个服务器环境配置和端口的设置，我现在就是在配置方面把我会的写出来，一些我不会的我只能在过程中提一嘴，剩下的就靠大家自己了。
***
[^1]: 关于设置端口这个操作我是不会的，我的队长是通过宝塔进行设置的，大家可以去学一下，宝塔进入方式  http://"你的域名”:8888 
 宝塔的默认端口就是8888，开头一定要用http而不是https。
 我把**换端口**的操作说一下：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415111709177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
①：刚进宝塔是要登录的，如上图：
获取默认账号密码：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415112545667.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
②：在目录——安全 看未使用的端口号
怎么设置端口号鹅不会，哈哈
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020041512365299.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
③：在目录——网站——设置——配置文件 找到location /
修改域名后面的端口号就可以了，我的是6666，想改成其他的就从未使用的端口号选一个。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415124038725.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
④：在目录——软件商店——Nginx——设置 选择重启
宝塔里的Ngnix安装挺简单的，在软件商店里找到nginx点击安装，等他安装完就ok了
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415124410532.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)
## 前端部分

```javascript
//.wxml
<view class="tui-picker-content">
<button bindtap="open">确认身份</button>
</view>
```

```javascript
//.js
wx.login({	//先调用 wx.login() 获取 临时登录凭证code 
      success: res =>{
        const code = res.code 	//获取到用户临时登录凭证code 
        wx.request({	//发送请求
          url: "http://“你的域名”?code="+code,	//携带code
          success: (res) =>{	//返回node请求到的OpenID与session_key
            const openid = res.data.openid
            wx.setStorageSync("openid", openid)
          }
        })
      },
      fail: console.log
    })

```
嗯，你没看错，前端跟官方文档差不多，没多点儿。
***
## 后端部分

```javascript
const express = require('express');
const request = require('request')

const app = express()



const appid = "wx123123123"  //开发者的appid
const appsecret = "123123123123"   //开发者的appsecret 登入小程序公共平台内查看

app.use('/login', (req,res)=>{
  const code = req.query.code //拿到传过来的code
  //调用 auth.code2Session接口，换取用户唯一标识 OpenID 和 会话密钥 session_key
  const url = `https://api.weixin.qq.com/sns/jscode2session?appid=${appid}&secret=${appsecret}&js_code=${code}&grant_type=authorization_code`

  request(url, (err,res,body)=>{
    res.send(body)  //将请求到的 OpenID与 session_key 返回给小程序页面js文件
  })

})
var server = app.listen(6666, function () {

  var host = server.address().address
  var port = server.address().port

  console.log("应用实例，访问地址为 http://%s:%s", host, port)
})
```
后端代码，也还是不多，跟在其他地方搜索的也差不多，还是这么简单。
***
这个困扰我好几天的问题就是这么简单！但是为啥困扰我这么多天呢？
就是万恶的配置问题！！！
我的代码其实在前两天的查找就完成了，但是一直报这个错：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200415131231566.jpg)
工具未检验合法域名这个警告不用在意，这个报错截图是我在和队长的聊天记录里找到的，之前的报错截图我都没保存……
我记得还有一个404 not found 的报错，但是出现频率没这个高。
这个报错是端口问题 ，我之前没设过端口，小程序默认的就是443，但是之后解决的过程中队长告诉我443被占用了，是因为Ngnix占用了443接口。之后队长在宝塔那边换了端口（这个换端口号的本质是把发送了443端口的请求通过nginx转接到http的6666端口）就解决了……，我一开始以为代码有问题熬夜找了两天，最后发现问题不在代码身上……，唉……

***
**总结**：代码方面其实不难，最重要的就是配置问题。合法域名、正确端口，解决了配置问题就问题不大了。希望和我情况一样的小伙伴能够找到我这篇博客，Emm……

如果有这个类型的其他问题或者发现文章中有错误地方的小伙伴欢迎私信或者评论

***
本篇博客代码部分参考博客
[https://blog.csdn.net/qq_41614928/article/details/90408891?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2](https://blog.csdn.net/qq_41614928/article/details/90408891?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2)

