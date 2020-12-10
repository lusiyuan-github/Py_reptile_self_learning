# Py_reptile_self_learning

<--关于python 爬虫自学过程中的重点-->

网页版： 下载zip后的  学学学.html    

[TOC]



------

## 零. 版权声明

本文章参考b站用户：**路飞学城IT** 旗下视频”2020年Python爬虫全套课程（学完可做项目）“总结归纳，仅供为个人学习使用。

[^]: 下文于（2020.12.7——2020.12.8）编写

## 一. 前提先知

### (1) robots.txt 协议

能爬取的数据：  在网址后加/robots.txt 查看网站允许爬的信息  （eg：https://www.baidu.com/robots.txt)

### (2) http 协议

服务器和客户端进行数据交互的一种形式

#### 常用请求头信息

User - Agent : 请求载体身份标识，也就是请求查询网站的浏览器信息或者编写的爬虫程序信息

Connection : 请求完毕后，是保持连接还是断开	

#### 常用响应头信息

Content - Type : 服务器响应回客户端数据类型（eg：float , json）

### (3) https 协议

安全的http（超文本传输）协议

### (4) 加密方式

#### 对称秘钥加密

客户端将密文和密钥一同交给服务器

若中途被拦截会直接解密，不安全

#### 非对称秘钥加密

服务器设定加密方式，将密钥告知客户端，客户端按照公钥编写生成密文交给服务端

效率低，若公钥被拦截修改仍存在问题

#### 证书秘钥加密

在非对称秘钥加密增加中间证书机构，先对设定公钥进行认证签名，双方均可信任，也就是https所用方式

## 二. request模块

### (1) 用途

基于网络请求的模块，用于模拟浏览器发起请求

### (2) 使用流程

#### 安装导入

win+r   cmd    pip install requests

```python
import requests
```

#### 指定url

（下面均以访问b站为例）

```python
url = 'https://www.bilibili.com/'
```

#### 发起请求

```python
response = requests.get(url=url)
# requests.get 返回一个响应对象
```

#### 获取响应数据

```python
page_text = response.text
print(page_text)
```

#### 持久化存储响应数据

```python
with open('./bilibili.html','w',encoding='utf-8') as fp:
    fp.write(page_text)
```

#### 完整代码

```python
import requests
if __name__ == '__main__':
    url = 'https://www.bilibili.com/'
    response = requests.get(url=url)
    # requests.get 返回一个响应对象
    page_text = response.text
    print(page_text)
    with open('./bilibili.html','w',encoding='utf-8') as fp:
        fp.write(page_text)
```

[^]: 下文于 2020.12.9 编写

### (3) 实战演练

#### 简易网页采集器

```python
#搜索b站网页
import requests

if __name__ == '__main__':
    #https://search.bilibili.com/all?keyword=python 这是在浏览器进行b站搜索python的url
    #可见在 ? 之前为主要url，后续keyword等为需要存入params字典的部分
    url = 'https://search.bilibili.com/all'
    kw = input("输入搜索：")
    param = {
        'keyword': kw
    }
    response = requests.get(url=url, params=param)
    # params存入的是字典，是url后续要增加的关键字
    page_text = response.text
    fileName = kw+'.html'
    with open(fileName, 'w', encoding='utf-8') as fp:
        fp.write(page_text)
    print("已经收集到"+kw+'数据')

```

##### UA伪装（反反爬策略）

程序编写时（爬虫时）的User - Agent 与浏览器直接访问时候User - Agent 不同。为了防止服务器监测时只允许浏览器模式的正常访问，而阻止一些爬虫的非正常访问（UA监测），故程序需要模拟某款浏览器进行访问，也就是需要进行UA伪装

```python
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.67 Safari/537.36 Edg/87.0.664.55'
    }
    #user-agent 在网页F12中Network里Request Headers里，本headers为Windows系统Microsoft Edge访问结果
```

##### 完整代码

```python
import requests

if __name__ == '__main__':
    url = 'https://search.bilibili.com/all'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.67 Safari/537.36 Edg/87.0.664.55'
    }
    kw = input("输入搜索：")
    param = {
        'keyword': kw
    }
    response = requests.get(url=url, params=param, headers=headers)
    # headers存入的是字典，是UA伪装时候的User——Agent
    page_text = response.text
    fileName = kw + '.html'
    with open(fileName, 'w', encoding='utf-8') as fp:
        fp.write(page_text)
    print("已经收集到" + kw + '数据')

```





#### 破解百度翻译

##### 阿贾克斯请求

https://baike.baidu.com/item/ajax/8425 关于ajax请求的定义，在浏览器中f12的Network中XHR中可以查看为阿贾克斯请求生成的sug，其为post请求，url均为https://fanyi.baidu.com/sug

```python
import requests
import json#用json存储

if __name__ == '__main__':
    post_url = 'https://fanyi.baidu.com/sug'  # post 请求
    word = input("输入单词：")
    data = {
        'kw': word
    }  # 查询数据
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.67 Safari/537.36 Edg/87.0.664.55'
    }  # UA伪装
    response = requests.post(url=post_url, data=data, headers=headers)  # 发送请求
    # 由服务器Response Headers 中Content-Type: application/javascript可知返回为json数据
    dic = response.json()  # 确认为json用此方法，注意text不加（），json加（），返回为字典
    filename = word+'.json'
    fp = open(filename, 'w', encoding='utf-8')
    json.dump(dic, fp=fp, ensure_ascii=False)
    #ensure_ascii=False在含有中文时候不采用ascii编码

```

[^]: 下文于 2020.12.10 编写



##### 破解搜狗翻译（自我尝试）

```python
import requests
import json

if __name__ == '__main__':
    url = 'https://fanyi.sogou.com/reventondc/suggV3'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.57'
    }
    word = input("输入一个单词：")
    data = {
        'from': 'auto',
        'to': 'zh-CHS',
        'client': 'wap',
        'text': word,
        'uuid': 'c0dbe681-bbcb-4079-8d2e-eaa936baef24',
        'pid': 'sogou-dict-vr',
        'addSugg': 'on'
    }
    respond = requests.post(url=url,data=data,headers=headers)
    dic = respond.json()
    filename = word+'.json'
    with open(filename, 'w', encoding='utf-8') as fp:
        json.dump(dic['sugg'], fp=fp, ensure_ascii=False)
    # print(dic['sugg'])
```

