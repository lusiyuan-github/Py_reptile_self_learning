# Py_reptile_self_learning

<--关于python 爬虫自学过程中的重点-->

[TOC]



------

day1-2（2020.12.7——2020.12.8）

## 一.前提先知

### (1) robots.txt 协议

能爬取的数据：  在网址后加/robots.txt 查看网站允许爬的信息  （eg：https://www.baidu.com/robots.txt)

### (2) http 协议

​		服务器和客户端进行数据交互的一种形式

#### 常用请求头信息

User - Agent : 请求载体身份标识，也就是请求查询网站的浏览器信息或者编写的爬虫程序信息

Connection : 请求完毕后，是保持连接还是断开	

#### 常用响应头信息

Content - Type : 服务器响应回客户端数据类型（eg：float , json）

### (3) https 协议

安全的http（超文本传输）协议

### (4) 加密方式

对称秘钥加密（客户端将密文和密钥一同交给服务器）

非对称秘钥加密

证书秘钥加密（https所用方式）

s

