---
title: Python爬虫03-requests模块
date: 2020-12-01 15:36:24
tags:
categories: Python爬虫
toc: true
---
requests模块: python中原生的一款基于网络请求的模块，功能强大、简单便捷、效率高。  
<!--more-->
- 作用：模拟浏览器发请求
- 如何使用（request模块编码流程）
1. 指定url
2. 发起请求（get/post）
3. 获取响应数据
4. 持久化存储


🗒️ Note:
----
`requests.get.text()`方法有时会出现中文乱码现象，原因是python系统和欲解析网页的编解码方式不统一，可以用：
```python
import sys
import chardet
print (sys.getfilesystemencoding())
print ('Html is encoding by : %',chardet.detect(page_content))
```

来检测系统和网页的编解码方式。  
然后使用
```swift
get = requests.get(url = url, headers = headers)
get.encoding = 'GB2312'
```
进行解码方式的改变。

## 实战编码

### 需求一：爬取搜狗首页页面数据
```python
import requests
if __name__ == "__main__":
    # step 1:指定url
    url = 'https://www.sogou.com/'
    # step 2:发起请求
    # step 3:获取响应数据(get方法)
    response = requests.get(url=url)
    page_text = response.text
    print(page_text)
    # step 4:持久化存储
    with open('./sogou.html','w',encoding='utf-8') as fp:
        fp.write(page_text)
    print('finished')
```

### 需求二：爬取搜狗指定词条对应的搜索结果页面（简易网页采集器）
```python
import requests
if __name__ == "__main__":
    #UA伪装
    headers = {
        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.3 Safari/605.1.15'
    }

    url = 'https://www.sogou.com/web'
    #处理url携带的参数：封装到字典中
    kw = input('enter a word:')
    param = {
        'query':kw       
    }
    #对指定的url发起的请求对应的url时携带参数的，并且请求过程中处理了参数
    response = requests.get(url=url,params=param,headers=headers)

    page_text = response.text

    fileName = kw+'.html'
    with open(fileName,'w',encoding='utf-8') as fp:
        fp.write(page_text)
    print(fileName,'保存成功')
```

- 反爬机制：UA检测  
门户网站服务器回检测对应请求的UA（载体身份标识），如果检测到UA是某款浏览器，则认定请求为正常请求；如果检测到的UA不是某款浏览器，则认定请求为不正常请求（爬虫），服务器端拒绝该次请求
  - 反反爬策略：UA伪装  
    让爬虫对应的UA伪装成某款浏览器  
    将对应的`headers`中的`User-Agent`参数赋值为某款浏览器对应的值,并将`headers`参数传入`get`中
    
### 需求三：破解百度翻译
- 教程中方法已不适用，疑似因为百度翻译页面的更新，全学完后看能不能自己做出来
- 应在页面检查器中查看所需内容的信息（URL、是post还是get、请求携带的参数、response的类型等）
- 步骤：
  1. 指定url
  2. UA伪装
  3. 参数处理
  4. 发送请求
  5. 获取响应数据
  6. 持久化存储

### 需求四：爬取豆瓣电影分类排行榜中的电影详情数据

```python
import requests
import json
if __name__ == "__main__":
    #将检查器中url后的值复制过来，去掉?后面的内容
    url = 'https://movie.douban.com/j/chart/top_list'
    #UA伪装
    headers = {
        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.3 Safari/605.1.15'
    }
    #在最下方的“查询字符串参数”中的参数
    params = {
        'type': '24',
        'interval_id':'100:90',
        'action':'',
        'start': '0',
        'limit': '20',
    }
    #查到使用的是get请求
    response = requests.get(url=url,params=params,headers=headers)
    #查到返回的是json响应文件；这个json内容由[]包围，应该是一个list
    list_data = response.json()
    fp = open('./douban.json','w',encoding='utf-8')
    json.dump(obj=list_data,fp=fp,ensure_ascii=False)
    print('排行榜爬取结束')
```

### 需求五：爬取肯德基指定地点餐厅数

```python
import requests
import json
if __name__ == "__main__":
    url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword'
    headers = {
        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.3 Safari/605.1.15'
    }
    location = input('输入地点：')
    data = {
        'cname':'',
        'pid':'',
        'keyword': location,
        'pageIndex': 1,
        'pageSize': 10,
    }
    response = requests.post(url=url,data=data,headers=headers)
    fp = open('./'+location+'.json','w',encoding='utf-8')
    json.dump(obj=response.text,fp=fp,ensure_ascii=False)
    print('餐厅爬取成功')
```

### 需求六：爬取国家药监局化妆品许可证

直接get请求得到的页面不包括所想要的公司名称等信息，说明所需信息是动态加载出来的，动态加载的数据由ajax数据包构成，在浏览器页面检查器中的XHR页面显示

```python
import requests
import json

if __name__ == "__main__":
    headers = {
        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.3 Safari/605.1.15'
    }
    home_url = ' http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsList'
    home_param = {
        'on': 'true',
        'page': '1',
        'pageSize': '15',
        'productName': '',
        'conditionType': '1',
        'applyname': '',
        'applysn': '',
    }
    home_data = requests.post(url=home_url,data=home_param,headers=headers).json()
    home_data = home_data['list']
    id_list = []
    for companyInformation in home_data:
        id_list.append(companyInformation['ID'])
    
    detail_list = []
    detail_url = ' http://scxk.nmpa.gov.cn:81/xk/itownet/portalAction.do?method=getXkzsById'
    for id in id_list:
        detail_param = {
            'id': str(id)
        }
        detail_data = requests.post(url=detail_url,data=detail_param,headers=headers).json()
        detail_list.append(detail_data)
    
    fp = open('./药监局.json','w',encoding='utf-8')
    json.dump(detail_list,fp=fp,ensure_ascii=False)
```  

这个题的难点在于对页面的分析，首页上的公司列表是ajax动态数据，公司详情页中的信息也是ajax动态数据。