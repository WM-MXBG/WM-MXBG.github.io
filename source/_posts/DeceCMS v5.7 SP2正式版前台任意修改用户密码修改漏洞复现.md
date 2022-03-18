---
title: DeceCMS v5.7 SP2正式版前台任意修改用户密码修改漏洞复现
date: 2020-10-24 22:24:04
tags: 
- experiment
---

## 一、漏洞限制
1、只能影响前台账户
2、只能修改未设置安全问题的账户
## 二、影响版本
DeDeCMSV5.7SP2 正式版(2018-01-09)
## 三、漏洞复现
### 复现环境 
   PHP 5.6
   DeDeCMSV5.7SP2 正式版(2018-01-09)
   ### 复现工具
   ①  [DeDeCMSV5.7SP2 正式版(2018-01-09)](http://www.dedecms.com/products/dedecms/downloads/)
   ② HackBar/Max HacKBar 插件 （后者火狐专有）
   ③ Proxy SwitchyOmega 插件
   ④ Burp Suite 抓包工具
   ⑤ phpstudypro 建站工具
   ⑥ Google Chrome/Firefox 浏览器

   ### 复现过程
   #### 用phpstudypro建站
   (建站不一定要用这个工具)
   这个工具其实建站很简单，可以直接开启Apache和MySQL套件,如图：

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024200805926.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center "建站套件")

你也可以创个域名，如图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024200935495.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center "建站域名")

直接在第一行输入你想起的域名，eg：www.xxxxx.com;
**直接用ip和用域名走接下来的流程我都走过，发现走域名更方便简单一些，后续会详细说明**

*如果发现MySQL数据库开启了一会儿就又关闭,可以看一看我的另一篇[博客](https://blog.csdn.net/Reaper_MXBG/article/details/109081140)，里面有详解*
#### 安装DeDeCMS
流程如下
1、下载后把uploads文件移到phpstudy_pro文件的WWW文件下（用域名的需要把uploads文件放在以域名为文件名的文件夹下）

uploads文件夹
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024204359181.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center  "uploads文件夹
")
用ip和用域名的都把uploads文件夹放在WWW文件夹下，和index.html同级

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024204615907.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

然后用ip的直接访问http://127.0.0.1/uploads/install/index.php
用域名的直接访问http://www.week3.com/uploads/install/index.php
**记住我的链接都是符合我本地路径的，别完全照抄哦**
会出现如图所示画面

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024210427119.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

继续

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024210549922.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

继续

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024210621473.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

到这一步的的时候注意数据库密码和管理员密码，一般设成和账号名一致就行，方便记忆

继续

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024210847764.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

登录管理员后台（**这个链接记得保存，后面会用到**)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024211016524.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

在这里修改一下这个选项为“是”，不然没法注册会员。
然后

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024211431637.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

这个站点配置这里算是很关键的一步，你要把原来的ip改成你**无限局域网适配器 WLAN 的IPV4地址**，具体原因我不是很清楚，问朋友说是如果用127.0.0.1的那个ip会进行不停的循环，用Burp Suite抓不到包，所以后续网址的ip我都换成了我的本地ip。

#####  0x02 设置代理
为了抓包要设置代理，要用到Proxy SwitchyOmega这个插件，谷歌和火狐都有，自行下载。ip配置成127.0.0.1，端口号配置成8080，这个教程我就不教了哦，很简单的，直接在proxy上填上数字就可以。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024212338559.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

**后续的网页操作都是要在proxy的代理下进行的，如图选项**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024215337834.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)


#### Burp Suite的准备
安装的教程我推荐一个[博客链接](https://blog.csdn.net/LUOBIKUN/article/details/87457545?utm_source=app)，安路径推荐不含中文，因为路径有中文大概率出问题。
安装之后就是如何抓包了，打开走三步

离开

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024213036249.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

下一个

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024213102509.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

进入Burp

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024213126709.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

然后在到“代理——截断”这里，让状态处于“拦截禁用”的状态

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024213304372.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
#### 后续操作
进入网站首页进行注册操作

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020102421425923.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)



记住安全问题要选择“没安全提示问题”

完成注册后可打开前面保存的网站后台网址查看注册的会员的id，就是mid的数值，我注册了两个账号，所以我test(账号名没限制）的mid是3，**这个id记住，后面会用到**

接下来就需要用hackbar了，但是谷歌的hackbar需要破解，火狐的hackbar的破解方我也懒得搞，所以直接用火狐的Max HacKBar插件，功能和hackbar几乎一样

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024215739223.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

按照图里的步骤，先load，再点击post data，然后把dopost=safequestion&safequestion=0.0&safeanswer=&id= 你的用户id
这段链接粘贴在框内，id为你注册的用户的id（之前让记的)，最后点击Execution这步先别着急，还记得咱们之前打开的处于“拦截禁用”状态的burp吗，点击转变为“拦截请求”状态，然后再点击“Execution”，此时你就会发现burp的界面上有了变化

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024220303625.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

这种界面不是咱们想要的，点击放包，可能需要点击多次，知道看到下面这个界面

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024220400360.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

咱们要的就是这个key，咱们把key=……的部分复制然后在原来id=2的后面加上&key=……，如图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024220927930.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

（不要在意为啥这张图片的key和抓到的不同，我截图不是一次截完的……)
或者你不复制key，直接一直点放包，最后链接上会出现带key的链接，只不过需要你把多于的 'amp;'字段删掉，成为和上图一样的链接格式。

如果发现浏览器打不开链接那就换个浏览器，不过别忘了都开代理。

然后随便设密码你就会发现登录成功

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201024221319251.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

以上就是本次漏洞复现的全过程。
