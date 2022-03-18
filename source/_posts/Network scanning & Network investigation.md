---
title: Network scanning & Network investigation
date: 2020-11-07 16:42:01
categories: 
- Network Penetration
tags: 
- experiment
---

## 实验目的

* 理解Network scanning&Network investigation的作用；通过搭建Network penetration testing平台，了解并熟悉常用搜索引擎、扫描工具的应用，通过信息收集为下一步渗透工作打下基础。

## 系统环境
* Kali Linux 2；Windows
## 网络环境
* 交换网络结构
## 实验工具
* Metasploitable2（虚拟机镜像）；Nmap（Kali自带）；WinHex、数据恢复软件等
## 实验步骤
### 文档查找
用搜索引擎Google或百度搜索麻省理工学院网站中文件名包含“network security”的pdf文档
搜索："network security" filetype:pdf site:www.mit.edu/

**谷歌**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101211755433.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101211800338.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

**百度**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101211757637.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
### 地址查询
查询此图的地理位置信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101212120763.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

首先在马蜂窝上搜索到Le Trentehuit，可以直接得到地址的具体信息（可能因为网速的原因，一直没加载出来），可以看到图片的右边显示的地址具体信息——
地址：38 avenue de Suffren,75015 Paris, ,France

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101213418831.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

随后采用百度地图定位到巴黎进行搜索

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101213547531.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

找到之后通过图片确认

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101213625877.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
### 手机定位
采用1+品牌的机型进行实验，拨号界面进入手机信息工程模式，一次成功.（HW采用拨号进入的方法没有查到LAC和CID）

**1+手机的信息展示:**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101215204905.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

通过手机获取的LAC和CID查询位置信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101215204948.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### 编码解码
通过观察码 Z29vZCBnb29kIHN0dWR5IQ== 的格式发现是Base64格式的码（尾巴的‘==’），所以直接网站解码

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101215347832.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101215347838.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
### 地址信息
#### 查询MAC&IP地址
内网中捕获到一个以太帧，源MAC地址为：98-CA-33-02-27-B5；目的IP地址为：202.193.64.34。
查询：该用户使用的什么品牌的设备，访问的是什么网站？

**MAC地址查询**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101220543481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

**IP地址查询**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101221509972.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

#### 查询MAC&IP地址
访问https://whatismyipaddress.com得到MyIP信息，利用`ipconfig`(Windows)或`ifconfig`(Linux)查看本机IP地址。
答：两者值相同吗？如果不相同的话，说明原因。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101223119968.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101223119725.jpg#pic_center)

**不相同**

网站查询的是公网ip，ipconfig查询的是内网ip，访问网站的时候网络运营商做了NAT，甚至是动态的NAT，所以公网查询到的ip和内网查询到的是不一样的。

### NAMP使用
先用`ifconfig`查一下kali的ip，后面排除用。（其实也没啥用(*^__^*) 嘻嘻……）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201105195304247.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

然后通过ip和子网掩码，再加上nmap命令查看子网内存活的主机，命令`nmap -sP`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201105200727567.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

通过挨个测试来确定哪个是要渗透的ip，可以通过ip加路由的方式看哪个链接能登录网页

#### 利用NMAP扫描Metasploitable2的端口开放情况并说明其中四个端口的提供的服务，简要说明该服务的功能
通过命令`nmap -u`扫描

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201105201648463.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

**端口nfs服务**

通过网络，让不同的机器、不同的操作系统可以共享彼此的文件。NFS服务器可以让PC将网络中的NFS服务器共享的目录挂载到本地端的文件系统中，而在本地端的系统中来看，那个远程主机的目录就好像是自己的一个磁盘分区一样，在使用上相当便利。

**端口postgresql服务**

postgres 是 PostgreSQL 数据库服务器。 客户端应用程序为了访问数据库，将通过 TCP Socket 或 Unix domain socket 连接到一个运行中的 postgres 进程。 然后该 postgres 实例将启动(fork)一个新的、独立的服务器进程来处理这个连接。

**端口netbios-ssn服务**

139是NETBIOS Session Service端口,用于提供文件共享服务,主要用于企业内部网之间互相
访问,如文件共享或打印等等.若你是单机在互联网上,最好还是将139关了,因为你几乎用不
到;若你在企业内部网,就大可不必,因为关掉后没多大意思,非等闲之人也进不了你的企业网。

**端口ccproxy-ftp服务**

用于局域网内共享宽带上网，ADSL共享上网、专线代理共享、ISDN代理共享、卫星代理共享、蓝牙代理共享和二级代理等共享代理上网。

#### 利用NMAP扫描Metasploitable2的操作系统类型
使用命令`nmap -O`检测Metasploitable2操作系统

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201105204248260.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
#### 利用NMAP穷举 Metasploitable2上 dvwa 的登录账号和密码
Nmap 自带有一些脚本，可以用来探测登陆界面或blast登陆界面，首先判断 dvwa 在 80 端口上，用`http-auth-finder`脚本探测站点上的登录授权页面，发现`/dvwa/login.php`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201105204611508.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

尝试使用类似于`http-form-brute`的脚本blast出一些账户密码，`http-form-brute` 传参`--script-args=http-form-brute.path=/dvwa/login.php 192.168.10.227`进行blast，得到账号：**admin**密码：**password**

#### 查阅资料，永恒之蓝-WannaCry蠕虫利用漏洞的相关信息。

永恒之蓝利用工具会扫描开放445文件共享端口的Windows机器，所以如果用户开启445端口并且系统未打MS17-010补丁就会在无感状态下被不法分子向系统植入恶意代码。

例如：WannaCry事件中，不法分子用永恒之蓝攻陷一台机器后会向该机器继续下发并运行永恒之蓝利用工具，让被攻陷的机器继续去扫描其他开放445端口的机器的同时下发并运行勒索病毒，加密用户系统中的文件，达到蠕虫式的传播，从而对全球上百个国家造成巨大影响。

### 用ZoomEye查询设备安全问题
首先搜索西门子公司的工控设备,我这里搜索了两个

**① PLC**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020110321591113.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103220709439.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

**② app:"Siemens Building Technologies Climatix WEB Server"**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103220635525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103220552460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

### Winhex简单数据恢复和取证
#### WinHex修复图片
elephant.jpg不能打开了，利用WinHex修复
这是elephant.jpg原本的预览样式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101224247756.jpg#pic_center)

用WinHex打开elephant.jpg发现二进制的文件开头为**00 00**,不符合正常**FF D8**格式

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101224752333.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

所以改为**FF D8**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101225013655.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

另存为jpg文件，修复图片得（因为原图太大，我截图上传的）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101225253644.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
#### WinHex修复图片
查看笑脸照片所隐藏的信息

**笑脸原图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101225433194.bmp#pic_center)
用WinHex打开这个smile图片并翻到底发现 **tom is the killer** 这条隐藏信息。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201101225558887.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

#### U盘数据恢复
这个没什么技术含量，就是在网上找软件然后扫描U盘并进行恢复，下面是我使用的几款软件

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103213136194.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103213155572.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020110321320996.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

试用版不行，得开vip……

下一款

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103213314716.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103213322935.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201103213341652.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center)

还不是免费的……
