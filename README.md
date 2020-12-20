

# Py_reptile_self_learning

<--关于python 爬虫自学过程中的重点-->

<--由于爬虫有一定时效性，请自行判断是否有过时信息-->

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
url = 'https://www.bilibili.com/''
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

##### params和data区别

requests模块发送请求有data、params两种携带参数的方法。

params在get请求中使用，data在post请求中使用。

params是往url后面添加参数，data是成为表单形式，是一个request body。

![img](https://img-blog.csdn.net/20180207160701009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmppbmVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![img](https://img-blog.csdn.net/20180207160808762?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmppbmVuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

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

[^]: 2020 .12. 13

爬取豆瓣电影

```python
import requests
import json

if __name__ == '__main__':
    url = 'https://movie.douban.com/j/chart/top_list'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.60'
    }
    param = {
        'type': '24',
        'interval_id': '100:90',
        'action':'',
        'start':' 20',	#开始值
        'limit': '20'	#一次取出多少
    }
    respond = requests.get(url=url,params=param, headers=headers)
    respond_json = respond.json()

    with open('./douban.json', 'w', encoding='utf-8') as fp:
        json.dump(respond_json, fp=fp, ensure_ascii=False)

```

#### 爬取肯德基地址

```python
import requests
if __name__ == '__main__':
    url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.60'
    }
    name = input('输入查询城市：')
    data = {
        'cname':'' ,
        'pid': '',
        'keyword': name,
        'pageIndex': '1',
        'pageSize': '10',
    }
    respond = requests.post(url=url,data=data,headers=headers)
    respond_text = respond.text
    filename = name+'.txt'
    with open(filename,'w',encoding='utf-8') as fp:
        fp.write(respond_text)
```

#### NMPA化妆品许可爬取

在下述爬取中，打开html会发现请求异常，因为里面数据跟浏览网页不是相同的url，考虑ajax

```python
import requests
if __name__ == '__main__':
    url = 'http://scxk.nmpa.gov.cn:81/xk/'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.60'
    }

    respond = requests.post(url=url,headers=headers)
    respond_text = respond.text
    filename = '药监总局化妆品许可证.html'
    with open(filename,'w',encoding='utf-8') as fp:
        fp.write(respond_text)
```

可以看到对应url中没有企业信息：

![image-20201213191012541](C:\Users\卢思远\AppData\Roaming\Typora\typora-user-images\image-20201213191012541.png)

ajax请求：

```python
import requests
import json
if __name__ == '__main__':
    url = 'http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsList'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.60'
    }
    data = {
        'on': 'true',
        'page': '1',
        'pageSize': '15',
        'productName': '',
        'conditionType': '1',
        'applyname': '',
        'applysn': ''
    }
    respond = requests.post(url=url, data= data,headers=headers)
    respond_json = respond.json()
    filename = '药监总局化妆品许可证.json'
    with open(filename, 'w', encoding='utf-8') as fp:
        json.dump(respond_json,fp=fp,ensure_ascii=False)

```

可以看到在得到的json数据里面，每个厂家对应独有的一个ID，而打开该厂家详细信息，发现其url后面多增加的与ID相同，于是爬取详细页面信息便可以从ID考虑

```json
{
    "filesize":"",
    "keyword":"",
    "list":[
        {
            "ID":"49fd67573d6641fabb9987cb83b1d876",
            "EPS_NAME":"现代百朗德生物科技（江苏）有限公司",
            "PRODUCT_SN":"苏妆20180010",
            "CITY_CODE":"85",
            "XK_COMPLETE_DATE":{
                "date":11,
                "day":5,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607616000000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2023-05-27",
            "QF_MANAGER_NAME":"江苏省药品监督管理局",
            "BUSINESS_LICENSE_NUMBER":"91320684061855846T",
            "XC_DATE":"2020-12-11",
            "NUM_":1
        },
        {
            "ID":"ea278b325af543998b5e0a1c2cc482ba",
            "EPS_NAME":"英德市藻米美妆产业有限公司",
            "PRODUCT_SN":"粤妆20190114",
            "CITY_CODE":null,
            "XK_COMPLETE_DATE":{
                "date":10,
                "day":4,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607529600000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2024-06-05",
            "QF_MANAGER_NAME":"广东省药品监督管理局",
            "BUSINESS_LICENSE_NUMBER":"91441881MA522GUR4Q",
            "XC_DATE":"2020-12-10",
            "NUM_":2
        },
        {
            "ID":"d4f024a2c7764c0596ea0c30ba3f89a0",
            "EPS_NAME":"成都盛世名妆化妆品有限责任公司",
            "PRODUCT_SN":"川妆20160033",
            "CITY_CODE":"165",
            "XK_COMPLETE_DATE":{
                "date":10,
                "day":4,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607529600000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2021-12-06",
            "QF_MANAGER_NAME":"四川省药品监督管理局",
            "BUSINESS_LICENSE_NUMBER":"915101156630153180",
            "XC_DATE":"2020-12-10",
            "NUM_":3
        },
        {
            "ID":"0ab5512d518e4f7591b1ec160ad15d93",
            "EPS_NAME":"重庆碧海源日化用品有限公司",
            "PRODUCT_SN":"渝妆20160009",
            "CITY_CODE":"450",
            "XK_COMPLETE_DATE":{
                "date":9,
                "day":3,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607443200000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2021-11-02",
            "QF_MANAGER_NAME":"重庆市药品监督管理局",
            "BUSINESS_LICENSE_NUMBER":"91500113345941893U",
            "XC_DATE":"2020-12-09",
            "NUM_":4
        },
        {
            "ID":"99b86a2a99c24d3bb292ae85070007d4",
            "EPS_NAME":"哈尔滨北星药业有限公司",
            "PRODUCT_SN":"黑妆20180001",
            "CITY_CODE":"266",
            "XK_COMPLETE_DATE":{
                "date":9,
                "day":3,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607443200000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2023-03-05",
            "QF_MANAGER_NAME":"哈尔滨新区管理委员会行政审批局",
            "BUSINESS_LICENSE_NUMBER":"91230109MA1CAW3J6T",
            "XC_DATE":"2020-12-09",
            "NUM_":5
        },
        {
            "ID":"8506f601a98341449960e330f1a8144e",
            "EPS_NAME":"深圳市纳家生活科技有限公司",
            "PRODUCT_SN":"粤妆20200244",
            "CITY_CODE":null,
            "XK_COMPLETE_DATE":{
                "date":8,
                "day":2,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607356800000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2025-12-07",
            "QF_MANAGER_NAME":"广东省药品监督管理局",
            "BUSINESS_LICENSE_NUMBER":"91440300MA5ET4B082",
            "XC_DATE":"2020-12-08",
            "NUM_":6
        },
        {
            "ID":"e3df927a54034119bdb5247b66b04ef5",
            "EPS_NAME":"广东御芳泽生物科技有限公司",
            "PRODUCT_SN":"粤妆20200243",
            "CITY_CODE":null,
            "XK_COMPLETE_DATE":{
                "date":8,
                "day":2,
                "hours":0,
                "minutes":0,
                "month":11,
                "nanos":0,
                "seconds":0,
                "time":1607356800000,
                "timezoneOffset":-480,
                "year":120
            },
            "XK_DATE":"2025-12-07",
            "QF_MANAGER_NAME":"广东省药品监督管理局",
            "BUSINESS_LICENSE_NUMBER":"91440605MA54WN4K67",
            "XC_DATE":"2020-12-08",
            "NUM_":7
        }
```

可是经过访问对应url发现，其增加ID后的页面仍然为ajax请求的数据，观察post请求里data发现，里面data只有ID一个值，不同的页面的ajax请求的url都为http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsById于是可以得出最终程序。

```python
import requests
import json
if __name__ == '__main__':
   url = 'http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsList'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36 Edg/87.0.664.60'
    }
    data = {
        'on': 'true',
        'page': '1',
        'pageSize': '15',
        'productName': '',
        'conditionType': '1',
        'applyname': '',
        'applysn': ''
    }
    respond = requests.post(url=url, data=data, headers=headers)
    respond_json = respond.json()
    id_list = []    #存储企业ID
    for dic in respond_json['list']:
        id_list.append(dic['ID'])
    print(id_list)
    post_url = 'http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsById'
    for id in id_list:
        data ={
            'id':id
        }
        detail_json = requests.post(url=post_url,data=data,headers=headers).json()
        print(detail_json)
    with open('./数据.json','w',encoding='utf-8') as fp:
        json.dump(detail_json,fp=fp,ensure_ascii=False)
```

## 三. 数据解析

通常情况爬取下来的整张页面，我们只需要其中特定部分的数据，所以需要数据解析。

故在持久化存储之前，加入数据解析步骤。

后续部分关系到正则表达式（暂时不会，补习python基础中）

[^]: 开始托更qwqwq 2020.12.20 ， 转向学习python 基础知识。



