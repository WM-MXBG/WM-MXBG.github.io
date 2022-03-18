---
title: phpStudy Pro的数据库启动一会儿又关闭的解决方法
date: 2020-10-14 19:50:09
tags: 
- solution
- note
---

## 问题本质

这个问题的本质其实是端口被占用（至少我遇到的情况是这样去）

## 解决方法步骤
### 打开界面上mysql的配置

如图所示：

![MYSQL配置](https://img-blog.csdnimg.cn/20201014192538904.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center "MYSQL配置界面")
### 打开错误日志选项
![错误日志路径](https://img-blog.csdnimg.cn/20201014192743692.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center"错误日志路径")
### 通过路径找到错误日志
![ibdata1.err文件](https://img-blog.csdnimg.cn/20201014192929842.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center "ibdata1.err")
### 打开错误日志发现问题所在——端口占用
![3306端口被占用](https://img-blog.csdnimg.cn/20201014193221415.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center "3306端口被占用")
### 解决端口占用
解决端口占用这么简单的操作我就不截图展示了（绝对不是我懒！)
命令就那么几条,不过一定要用**管理员模式**开启cmd：
#### 查询占用端口的PID

```javascript
netstat -ano|findstr 3306	
//这里的3306是我的被占用的端口，每个人可能不同
```

#### 查询占用端口的程序

```javascript
tasklist|findstr "25484"
//25484还是我的，还是因人而异，哈哈
```
查询到占用的程序是mysql数据库（我觉得一般都是自己之前建的数据库在占用）

![占用端口的程序](https://img-blog.csdnimg.cn/2020101419402559.jpg#pic_center "mysqld占用端口")
### 释放它！

```javascript
//你直接杀也行
taskkill /pid 25484 /t /f
//或者关闭相应程序也行
taskkill /f /t /im mysqld.exe
```
结果

![程序终止](https://img-blog.csdnimg.cn/20201014194542110.jpg#pic_center "程序终止")
![大功告成](https://img-blog.csdnimg.cn/20201014194634611.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlYXBlcl9NWEJH,size_16,color_FFFFFF,t_70#pic_center "大功告成")

## 总结
学会找错误日志，学会科学上网（先上网找答案，找不到再说，哈哈哈）。
