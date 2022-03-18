---
title: CTF Practice
date: 2020-12-15 23:11:44
categories: 
- Network Penetration
tags: 
- experiment
---

## 实验目的
通过对目标靶机的渗透过程，了解CTF竞赛模式，理解CTF涵盖的知识范围，如MISC、PPC、WEB等，通过实践，加强团队协作能力，掌握初步CTF实战能力及信息收集能力。熟悉网络扫描、探测HTTP web服务、目录枚举、提权、图像信息提取、密码破解等相关工具的使用。
## 系统环境
Kali Linux 2、WebDeveloper靶机来源：https://www.vulnhub.com/ 
## 实验工具
不限
## 实验步骤和内容
目的：获取靶机Web Developer 文件文件/root/flag.txt中flag。
基本思路：本网段IP地址存活扫描(netdiscover)；网络扫描(Nmap)；浏览HTTP 服务；网站目录枚举(Dirb)；发现数据包文件 “cap”；分析 “cap” 文件，找到网站管理后台账号密码；插件利用（有漏洞）；利用漏洞获得服务器账号密码；SSH 远程登录服务器；tcpdump另类应用。
实施细节如下：

### 发现目标，找到WebDeveloper的IP地址
简单的`nmap -sP`命令（怎么获取ip我就不发了……，前面博客有）,至于怎么确定是这个ip的，除了ip排除法，就是拿链接访问网址验证。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215154402907.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### 利用NMAP扫描目标主机，发现目标主机端口开放、服务情况

关于扫描的命令我当时突发奇想的想到能不能多点样式，于是就用了三个命令：
第一个：`nmap`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215184238935.jpg#pic_center)

第二个：`nmap -u`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215184304191.jpg#pic_center)

第三个：`nmap -sS -sV -TS -A -p-`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215184315717.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

提供的服务：**ssh** 和 **http**

### 若目标主机提供了HTTP服务，尝试利用浏览器访问目标网站
没什么好说的，直接访问ip。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215184801733.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215184843759.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)



### 利用whatweb探测目标网站使用的CMS模板并分析使用的CMS是什么
使用命令 `whatweb`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215184933344.jpg#pic_center)

注意图中划红线的部分，表明使用的CMS模板为**WordPress**。

### 简要说明wpscan功能
WPScan是Kali Linux默认自带的一款漏洞扫描工具，它采用Ruby编写，能够扫描WordPress网站中的多种安全漏洞，其中包括主题漏洞、插件漏洞和WordPress本身的漏洞。
[详情链接](https://www.freebuf.com/sectool/174663.html)

### Dirb 爆破网站使用目录，找到一个似乎和网络流量有关的目录（路径）
命令 `dirb`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215203045612.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

通过`ipdata、sever-status`字眼判断。

### 浏览器访问该目录（路径）
访问目录直接找到cap文件！

![](https://img-blog.csdnimg.cn/20201215203404162.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### 用Wireshark分析该数据包，分析TCP数据流
因为登录注册这些操作都是客户发给服务器，所以我们通过`http.request.method == POST`命令筛选，再从`Info`信息段中发现`login`字眼，则直接查到账号密码。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215205708880.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### 利用上一步得到的信息进入网站后台

注意我的链接 `/wp-login.php`，剩下的就是直接上号。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215210106360.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### 利用该CMS存在的（插件Plugin）漏洞

这个漏洞嘛，我没深研究，只是知道有这个漏洞就可以坏事了，嘿嘿嘿。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215222415511.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)


### 利用该插件漏洞提权
我们这里采用的是上传反弹shell的方法，所以我们需要一个可以反弹我们上传的shell的页面。不过在选页面之前我们先下载这个可以反弹TCP连接到攻击者的脚本，下载地址 [http://pentestmonkey.net/tools/web-shells/php-reverse-shell](http://pentestmonkey.net/tools/web-shells/php-reverse-shell)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215212725790.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

下载完解压是这样

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215212746301.jpg#pic_center)

这个php就是我们需要的，不过它还需要初始化（这个初始化你可以在本地改，或者粘贴到页面后再改，不过这里建议直接在本地初始化，这样我们后续的试验中就不用担心代码每次复制粘贴都需要初始化的困扰）

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121521300365.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

**↑**把ip改为攻击方，即我们自己的ip；port端口号随意改。

现在准备工作完成了，我们可以来改页面了↓

通过**第9步**的登录我们成功进入了后台，然后我们在左侧目录里的Appearance→Editor里随便找一个页面，一般用404界面↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/202012152116354.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

把php脚本里的代码粘贴到404页面后直接点击下方的Updata File（蓝色按钮）会报错。这时就需要我们进行更改，即更改右上方的edit后再点击按钮更新文件↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215213331854.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

这是更改版本27为26后的提交，成功了。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215213708294.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

然后要注意Appearance→Themes界面，这里选定的还是27版本，我们要把这里也更新为26版本 。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215213753507.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

如果不更改Themes，则会在访问404页面时出现这样的错误情况。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121521403826.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

而更改后的Themes界面是这样的。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215214100851.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

访问404页面成功是这样的。↓

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215214243312.jpg#pic_center)

#### 补充
==扩==：我在这里补充一下我做这个步骤的一些个人经验。
1. 咱们先说说这个404页面，404页面代表不存在，那我们在ip后随便接个路由，就比如`http://192.168.238.134/404.php`，得到的页面是这样的。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215214617822.jpg#pic_center)

  大家注意，这个404是Apache给的，不是这个网站搭建时设置的页面，所以访问这种页面时没法继续实验的。
  还有就是怎么确定网站搭建的404页面路径。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215214837730.jpg#pic_center)

  说实话，我在网站后台找半天也没找到404页面的路径，这个我之所以能访问到是因为指导书上有……，我咨询别人，给我的答案就是逻辑推理……emm。推理就是从咱们**第六步Dirb爆破网站使用目录**得到的那张图↓开始……emm。

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215215014382.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  这种方法我不是很想用，所以我又用了一种**新的方法**实现这个步骤，就不用404页面了，后面我会说的。

2. 第二个就是版本更换问题，即27和26之间的爱恨情仇，为啥更换就好使的原因我没有去深究，不过知道更换版本好使对于这个实验就够用了。
    其实不是非得都保持26，这个看你更改时的版本是多少，比如说你要粘贴代码时右上角版本是27，更新文件会失败，这时就要更改版本为26；如果粘贴代码时右上角是26，更新文件也会失败，这时就要更改版本为27。至于版本25在我的实验中一直出问题。

3. 下面我就要介绍我自己做这个实验步骤的方法了。
既然404页面的路径不好推，那咱们就自己选定一个页面，比如说我在这里选定page.php界面。↓

4. ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215223326743.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  咱们在Page里添加新页面。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215223331430.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  命名完页面名后（不是一定要和文件名一个名），选择路径。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215223621100.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  然后就是熟悉的更改版本，粘贴代码，更新文件。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121522370285.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  最后我们可以通过`Preview`访问页面。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215223743430.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  结果如下。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215223901746.jpg#pic_center)也可以通过路径来访问，结果依旧成功。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215223928745.jpg#pic_center)

  不过我们要注意，不论是用404页面还是自己选的页面，==在访问前在Kali中利用NC开始监听，攻击者浏览器访问修改的PHP页面。从而得到反弹shell==。

  开始监听↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215224314859.jpg#pic_center)

  

  访问页面后所监听到的。↓

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215224203865.jpg#pic_center)

  然后找到wp-config.php文件
  （网站模板建立时的路径一般是固定的，所以我们直接cd过去就行)
  命令 `cd /var/www/html`![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215224643623.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  命令 `ls`

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215224655170.jpg#pic_center)

  建立会话 `cat wp-config.php`

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215225740435.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### 12. SSH登录服务器
尝试查看/root/flag.txt

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215230041101.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

管理员命令也不行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215230125420.jpg#pic_center)

都没办法查看。↑

### 13. 使用tcpdump执行任意命令
（当tcpdump捕获到数据包后会执行指定的命令。）
查看当前身份可执行的命令。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215230211856.jpg#pic_center)

既然可以用root权限执行tcpdump命令 `touch /tmp/exploit` 那可以创建攻击文件了。↓
（创建后我查看了一番，找到了创建的文件）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215230307212.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

下面三行命令分别是

1. 写入shellcode `echo 'cat/root/flag.txt' > /tmp.exploit`；

2. 赋予可执行权限 `chmod +x/tmp/exploit`；

3. 利用tcpdump执行任意命令 `sudo tcpdump -i eth0 -w /dev/null -w 1 -G 1 -z /tmp/exploit -Z root`

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201215230442194.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

  最后得到图中红色方框内的结果。↑

