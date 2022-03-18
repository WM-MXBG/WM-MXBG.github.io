---
title: Network sniffing & identity authentication
date: 2020-11-10 11:50:56
categories: 
- Network Penetration
tags: 
- experiment
---

## 实验目的
* 通过使用Wireshark软件掌握Sniffer（嗅探器）工具的使用方法，实现捕捉HTTP等协议的数据包，以理解TCP/IP协议中多种协议的数据结构、通过实验了解HTTP等协议明文传输的特性
* 研究交换环境下的Network sniffing实现及防范方法，研究并利用ARP协议的安全漏洞，通过Arpspoof实现ARP欺骗以捕获内网其他用户数据
* 能利用BrupSuite实现网站登录暴力破解获得登录密码
* 能实现ZIP密码破解，理解安全密码的概念和设置
## 系统环境
* Kali Linux 2、Windows
## 网络环境
* 交换网络结构
## 实验工具
* Arpspoof、WireShark、BurpSuite、fcrackzip（用于zip密码破解）
## 实验步骤和内容
### 网络嗅探部分
* 网络嗅探：Wireshark 监听网络流量，抓包。
* ARP欺骗： ArpSpoof，实施ARP欺骗。
* 防范： 防范arp欺骗。

#### sniffer(Wireshark)抓包
这里选择kali为攻击方主机A，Metasplotis为被抓方主机B。
这里先查一下A主机的ip

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020110816021192.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
然后`nmap -sP`来看一下B主机和A主机是否在一个网段

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108160416449.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

然后A尝试一下能不能ping通B

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108160405983.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

ping通了，然后当时我想既然在一个网段了那B应该也能ping通A，而且这样也能抓到包，两全其美！（虽然不太符合原意)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108160730860.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

果然能ping通
然后用Wireshark抓一下刚才B ping A的包，并用过滤语句`ip.src == 192.168.238.129`来过滤一下

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108160840731.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

完成。

#### ARP欺骗
1. **为了捕获A到外网的数据，B实施ARP欺骗攻击，B将冒充该子网的什么实体？**

   > 主机B冒充的是子网中的网关

2. **写出 arpspoof 命令格式&B是否能看到A和外网的通信（A刚输入的帐户和口令）&在互联网上找到任意一个以明文方式传递用户帐号、密码的网站，截图Wireshark中显示的明文信息。**

   在这次实验中kali为截取信息的主机B，Win7主机是发送信息的主机A，两个主机在同一个局域网下，主机B冒充网关
   因为我新装的kali，没有arpspoof，所以我又装了一个

   > `sudo apt-get install dsniff`

   开启端口转发，允许主机B转发A发送的数据包

   > `ech0 1 > /proc/sys/net/ipv4/ip_forward`

   在攻击之前查一下对方主机的ip
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110104317987.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   再查一下局域网的网关![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110104343208.jpg#pic_center)
   随后开始arp欺骗，arp欺骗的命令格式为

   > `arpspoof -i etho -t ip_1 ip_2`

   ==ip_1：主机A的ip==
   ==ip_2：网关ip==

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110104830181.jpg#pic_center)

   然后我们用主机A(Win7)访问某生活网

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110104942318.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

   看一下主机B的Wireshark，筛选规则`http.request.method == POST`

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110105105999.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

   成功抓到数据

#### FTP数据还原部分
1. **FTP服务器的IP地址是多少？你是如何发现其为FTP服务器的？**

   用ftp作为条件过滤，发现大量基于FTP协议传输的数据包，所以推测是有FTP服务器的。

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108161457181.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   服务器会响应用户的请求，于是用 ‘response’ 过滤

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108161550857.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   发现response的ip原地址都是192.168.182.1，所以ftp的ip地址是192.168.182.1

2. **客户端登录FTP服务器的账号和密码分别是什么?**

   账号和密码指定是用户发送给ftp服务器的，所以可以用`ftp && ip.src == 192.168.182.132`过滤，并搜索'request'字段

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108162254246.jpg#pic_center)

   可得账号：student，密码：sN46i5y。

3. **客户端从FTP下载或查看了2个文件，一个为ZIP文件，一个为TXT文件，文件名分别是什么？**

   先发一下几个常用文件格式的文件头/文件尾

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108162607333.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   
   所以搜索zip文件就直接搜索十六进制码‘50 4B 03 04’，找到文件 '1.zip'
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108162706949.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   
   > 每个数据包的长度都是固定的，这是因为网络传输的过程中会限制每次传输的数据流长度，对任一数据包采用追踪数据流就可得到完整数据。
   
   随后我们将原始数据另存为1.zip
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108164500608.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

   然后打开1.zip文件，发现里面有张图片，但是需要密码（有锁的图标)
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108164603909.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

   在这里采用暴力破解工具fcrackzip，但是我在安装的时候出了一些问题，如下：
   这是正常的安装指令`sudo apt-get install fcrackzip`

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108165732897.jpg#pic_center)
   
   出错的原因是我的软件源还没有更新，更新命令`sudo apt-get update`
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108170100453.jpg#pic_center)

   随后再安装fcrackzip 
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108170139204.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   
   随后进行暴力破解，我这里有一些常用命令，分享一下

   
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108170351636.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   
   破解命令为 `fcrack -v -b -c1 -l 6 -u 1.zip`

   
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108170504276.jpg#pic_center)
   
   解锁可得到企鹅图片
   
   
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108170556507.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   
   txt文件直接搜索'txt'字段可得 '复习题.txt'
   
   
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108170837539.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
   注意要找到FTP-DATA流的 '复习题.txt',只有这个流的能查看到信息（==上文中的zip也是这样==)
   
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108171746494.jpg#pic_center)

### 网站密码破解部分
* 利用人们平时常用的词、句破译，如果说暴力破解是一个一个的尝试那么字典破译就是利用人们习惯用人名、地名或者常见的词语设置成密码的习惯进行破译。字典破译速度比暴力破译更快但是有时候密码设置中包含了没有字典库中的词句就无法破解出来了，因此有好的字典是关键。
以*****为目标网站，构造字典（wordlist），其中包含你的正确密码，利用burpsuite进行字典攻击，实施字典攻击，你是如何判断某个密码为破解得到的正确密码，截图。

此次目标网站是某大学学生选课网址，工具使用burpsuite

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108224311662.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

在确认网站代理后，输入账号和一个随便的密码，点击登录，bp就会抓到包

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108230000304.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

开始准备爆破

攻击类型选择Sniper就行，clear一下再选取密码部分再点击add

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201125211813233.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

因为我临时随便设了个6位纯数字密码，所以我的有效载荷类型选的就是数值（Number），范围是000000-999999，每次进1，数字格式像图里一样就行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108230700944.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

我为了快点，进程数调到了10

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201109213001113.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

随后开始攻击

>测试的时候知道密码就不用把范围像我这样设得这么广，很费时间的。

结果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201109213202866.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

正确密码的长度和别的长度是不一样的

然后我又采用了构造字典的方法再破解了一次，载荷类型默认简单清单，把自己构造的包含正确密码的字典载入

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110114649518.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201110114803484.jpg#pic_center)

结果ok

#### MD5解密
SqlMap得到某数据库用户表信息，用户口令的MD5值为7282C5050CFE7DF5E09A33CA456B94AE
那么，口令的明文是什么？

我采用的是网站破解密码的方式，一共访问了四个网站：

* 成功网站：

 	[https://www.somd5.com/](https://www.somd5.com/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108172822358.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

明文内容为 iampotato

* 收费网站：
			
	[https://cmd5.la/user/index.php](https://cmd5.la/user/index.php)

	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108172901915.jpg#pic_center)

	
	[https://www.cmd5.com/](https://www.cmd5.com/)


![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108172918608.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)


* 失败网站：

 	[https://www.sojson.com/encrypt_md5.html](https://www.sojson.com/encrypt_md5.html)


​	
 	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201108172937345.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
#### John the Ripper的作用
John the Ripper免费的开源软件，是一个快速的密码破解工具，用于在已知密文的情况下尝试破解出明文的破解密码软件，支持目前大多数的加密算法，如DES、MD4、MD5等。它支持多种不同类型的系统架构，包括Unix、Linux、Windows、DOS模式、BeOS和OpenVMS，主要目的是破解不够牢固的Unix/Linux系统密码。

### 问题思考
#### 如何防止ARP攻击
很多人的博客缩写的防止arp攻击方法一般都是这三种
* 双绑措施
* ARP个人防火墙
* VLAN和交换机端口绑定

不过我还找到了解决ARP最根本的方法——免疫网络

免疫网络就是在现有的路由器、交换机、网卡、网线构成的普通交换网络基础上，加入一套安全和管理的解决方案。这样一来，在普通的网络通信中，就融合进了安全和管理的机制，保证了在网络通信过程中具有了安全管控的能力，堵上了普通网络对安全从不设防的先天漏洞。

[参考链接](https://www.jianshu.com/p/c04c76e2fe96)
#### 安全密码(口令)应遵循的原则
* 避免出现弱密码
* 方便用户记忆
* 不同平台不建议使用相同密码
#### 字典攻击中字典的重要性
字典针对弱密码、规律密码、带有特殊字段的密码的攻击很有效，暴力破解这些密码很有可能成功。此外，字典配和社会工程学的应用效果绝对1+1>2，能提高暴破的成功率。
### 实验小结
* 学会了arp欺骗
* 知道了ftp传输协议
* 知道了arp攻击原理
* 学会了新的Wireshark过滤规则
* 学会了用bp暴力破解(部分)

收获满满，233。
