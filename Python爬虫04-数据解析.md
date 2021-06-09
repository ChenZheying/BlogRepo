---
title: Python爬虫04-数据解析
toc: true
date: 2020-12-03 14:37:26
tags: 正则表达式
categories: Python爬虫
---
在聚焦爬虫（爬取页面中指定的页面内容）中，可以先将整张页面爬取，再进行数据解析。数据解析可以通过正则表达式、bs4、**xpath**三种方法实现。
<!--more-->
- 数据解析原理：  
    要解析的局部文本内容存储在标签之间或标签中的对应属性中，因此，数据解析的步骤：  
    1. 进行指定标签的定位
    2. 标签或者标签对应属性中存储数据值的提取

- 聚焦爬虫编码流程：
  1. 指定url 
  2. 发起请求
  3. 获取响应数据
  4. 数据解析
  5. 持久化存储

## 正则表达式

用于在大段文字中匹配**指定格式**的字符串。  
练习网站：
1. www.regexr.com
2. https://regex101.com/

<details>
  <summary>常用正则表达式</summary>

|表达式|表达式意义|其他|
|-----|--------|---|
|`.`|任意字符||
|`\.`|.||
|`\w`|阿拉伯数组、大小写英文字母、下划线||
|`\W`|`\w`匹配不到的所有字符|
|`\d`|数字|
|`\D`|非数字|
|`\s`|空白字符（空格、制表符、换行）|
|`\S`|空白字符以外的其他字符|
|`[abc]`|匹配出现在方括号中的任意一个字符|每个`[范围]`只匹配一个单个字符|
|`[a-z]`|匹配从a到z的26个小写字母||
|`[\4e00-\u9fa5]`|匹配所有的中文字符|[unicode字符查询网站](https://graphemica.com)|
|`[范围]+`|范围中的字符出现一次或多次||
|`[范围]*`|范围中的字符出现零次或多次||
|`[范围]?`|范围中的字符出现零次或一次||
|`[范围]{a,b}`|范围中的字符重复a到b次|`{}`内可以是`{a}{a,b}{a,}{,b}`|
|`\b`|单词边界||
|`()`|字符分组|有几个`()`就有几组，`$1`表示第一组，`$2`表示第二组|
|`(ab|cd|ef)`|匹配`ab`或`cd`或`ef`||
|`(?:模式)`|括号内仍是一组，但这组不被捕获，不用`$1`表示||
|`表达式(?=模式)`|正向肯定预查，匹配的整个字符串中应包括它，但它本身不进行匹配|例：`\d+(?=元)`可以匹配到`100元`中的`100`|
|`表达式(?!模式)`|正向否定预查，匹配的整个字符串中不应包括它|例：`\d+(?!元)`可以匹配到`100元`中的`10`|
| | |`\d+(?!元|\d)`不可以匹配到`100元`中的任意字符|
|`(?<=模式)表达式`|反向肯定预查|比正向多了一个`<`|
|`(?<!模式)表达式`|反向否定预查|比正向多了一个`<`|

</details>

### python中re模块常用方法
- re.match 从字符串的起始位置匹配一个个模式
- re.search 找到第一个
- re.compile 将一个正则表达式定义为对象
- re.findall 找到所有的
- re.sub 替换
- re.split 分割
在数据解析中使用re.S

### 需求：爬取糗事百科热图板块的所有图片
```python
import requests
import re
import os
#需求 ：爬取糗事百科糗图板块下所有的图片
if __name__ == "__main__":
    headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.3 Safari/605.1.15'
    }
    # 设置通用url模版
    url = 'https://www.qiushibaike.com/imgrank/page/%d/'
    for pageNum in range (1,3):
        new_url = format(url%pageNum)

        #使用通用爬虫对url对应的一整张页面进行爬取
        page_text = requests.get(url=new_url, headers=headers).text

        # 使用聚焦爬虫解析出所有糗图
        ex = r'<div class="thumb">.*?<img src="(.*?)" alt.*?</div>'
        img_src_list = re.findall(ex,page_text,re.S)
        if not os.path.exists('./qiutuLib'):
            os.mkdir('./qiutuLib')
        for src in img_src_list:
            src_url = 'https:'+src
            img_data = requests.get(url = src_url,headers = headers).content
            img_name = './qiutuLib/' + src.split('/')[-1]

            with open(img_name,'wb') as fp:
                fp.write(img_data)
                print('图片',img_name,'已保存')
```


## bs4数据解析
`python`独有的数据解析方式。

### 环境安装
```python
pip install bs4
pip install lxml
```
### bs4数据解析的原理
1. 实例化一个BeautifulSoup对象，并且将页面源码数据加载到该对象中。
2. 通过调用BeautifulSoup对象中的相关书香或方法进行标签定位和数据提取。
  
### BeautifulSoup使用
```python
from bs4 import BeautifulSoup
```

`lxml`是一个解析器。  

#### 对象实例化
- 加载本地html文档
    ```python
    fp = open('./test.html','r',encoding='utf-8')
    soup = BeautifulSoup(fp,'lxml')
    ```

- 加载互联网上获取的页面
    ```python
    page_text = response.text
    soup = BeautifulSoup(page_text,'lxml')
    ```


#### 用于数据解析的方法/属性

本小节中对于方法/属性的介绍以对./test.html的解析为例
```html
<html lang="en">
<head>
	<meta charset="UTF-8" />
	<title>测试bs4</title>
</head>
<body>
	<div>
		<p>百里守约</p>
	</div>
	<div class="song">
		<p>李清照</p>
		<p>王安石</p>
		<p>苏轼</p>
		<p>柳宗元</p>
		<a href="http://www.song.com/" title="赵匡胤" target="_self">
			<span>this is span</span>
		宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱</a>
		<a href="" class="du">总为浮云能蔽日,长安不见使人愁</a>
		<img src="http://www.baidu.com/meinv.jpg" alt="" />
	</div>
	<div class="tang">
		<ul>
			<li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷,路上行人欲断魂,借问酒家何处有,牧童遥指杏花村</a></li>
			<li><a href="http://www.163.com" title="qin">秦时明月汉时关,万里长征人未还,但使龙城飞将在,不教胡马度阴山</a></li>
			<li><a href="http://www.126.com" alt="qi">岐王宅里寻常见,崔九堂前几度闻,正是江南好风景,落花时节又逢君</a></li>
			<li><a href="http://www.sina.com" class="du">杜甫</a></li>
			<li><a href="http://www.dudu.com" class="du">杜牧</a></li>
			<li><b>杜小月</b></li>
			<li><i>度蜜月</i></li>
			<li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游,凤去台空江自流,吴宫花草埋幽径,晋代衣冠成古丘</a></li>
		</ul>
	</div>
</body>
</html>
```
- 标签定位

|方法/属性|简介|举例|
|:--|:--|:--|
|`soup.html标签名称`|返回html中的第一次出现的指定标签及其内容|`print(soup.div)`|
|`soup.find('html标签名称')`|和上面等同：返回html中的第一次出现的指定标签及其内容|`print(soup.find('div'))`|
|`soup.find('html标签名称',属性名称='属性值')`|**属性定位**：返回html中的第一次出现的、且属性为指定属性值的指定标签及其内容|`print(soup.find('div',class_='song'))`|
|`soup.find_all('html标签名称')`|返回包含对应名称的所有标签的列表|`print(soup.find_all('a'))`|
|`soup.find_all('html标签名称',属性名称='属性值')`|**属性定位**：返回html中的所有属性为指定属性值的指定标签及其内容||
|`soup.select('某种选择器(id, class, 标签...选择器)')`|返回符合要求的所有标签的列表|`soup.select('.tang')`|
|`soup.select('.tang > ul > li > a')`|`>`表示一个层级||
|`soup.select('.tang > ul a')`|空格可以表示多个层级||

- 提取文本内容/属性

|方法/属性|简介|举例|
|:--|:--|:--|
|`soup.标签.text`/`soup.标签.get_text()`|获取标签下的所有（直系以及隔层的）**文本内容**|`print(soup.find('a',class_='du').text)`|
|`soup.标签.string`|获取标签下的直系**文本内容**|`print(soup.find('div',class_="song").string)`返回`None`|
|`soup.标签['属性名']`|返回标签中属性名对应的属性值|`print(soup.select('.tang > ul a')[0]['href'])`|

### 需求：爬取三国演义小说所有的章节标题和章节内容
页面：https://www.shicimingju.com/book/sanguoyanyi.html

```python
from bs4 import BeautifulSoup
import requests

if __name__ == "__main__":
    
    # 对首页的页面数据进行爬取
    headers = {
        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.3 Safari/605.1.15'
    }
    url = 'https://www.shicimingju.com/book/sanguoyanyi.html'
    page_text = requests.get(url=url,headers= headers).text
    soup = BeautifulSoup(page_text,'lxml')
    li_list = soup.select('.book-mulu > ul > li')
    fp = open('./三国演义.txt','w', encoding='utf-8')
    for li in li_list:
        title = li.string
        #print(title)
        detail_url = 'https://www.shicimingju.com'+li.find('a')['href']
        detail_text = requests.get(url=detail_url,headers=headers).text
        detail_soup = BeautifulSoup(detail_text,'lxml')
        #print(detail_soup)
        detail_content = detail_soup.find('div', class_ = "chapter_content").text
        fp.write(title+detail_content+'\n')
        print(title,'保存成功')
```

## xpath数据解析
最常用且最便捷高效的一种解析方式，且通用性较强，可在其他语言中应用。

### 环境安装
```python
pip install lxml
```
### xpath解析原理
1. 实例化一个etree的对象，且需要将被解析的页面源码数据加载到该对象中。
2. 调用etree对象中的xpath方法结合xpath表达式实现标签的定位和内容的捕获。

### etree使用
```python
from lxml import etree
```
#### etree对象实例化
- 将本地的html文档中的源码数据加载到etree对象中
    ```python
    etree.parse(filePath)
    ```
- 将从互联网上获取的源码数据加载到该对象中
    ```python
    etree.HTML('page_text')
    ```

#### xpath表达式

- xpath表达式只可以根据层级关系进行标签定位
- xpath表达式的返回结果是一个列表

|表达式|功能|例子|
|:--:|:--:|:--:|
|`/`|表示一个层级|`/html/body/div/p`返回此层级下的所有标签|
|`//`|表示多个层级|`/html//p`|
|`标签名[@属性名="属性值"]`|属性定位|`//div[@class="song"]`|
|`层级[索引号]`|返回该层级下指定索引对应的内容，索引号从`1`开始|`//div[@class="song"]/p[2]`|
|`/text()`|获取标签的直系文本内容列表|`//div[@class="song"]/p[2]/text()`|
|`//text()`|获取标签的所有（非直系）文本内容列表|`//div[@class="song"]//text()`|
|`/@属性名`|返回对应属性值|`//div[@class="song"]/img/@src`|

### xpath案例-58二手房
需求：解析出 https://yuncheng.58.com/ershoufang/?PGTID=0d100000-0161-5949-8a47-676a0ba9f986&ClickID=2 中的所有房源名称，并进行持久化存储

```python
import requests
from lxml import etree
if __name__ == "__main__":
    # 爬取页面源码数据
    url = 'https://yuncheng.58.com/ershoufang/?PGTID=0d100000-0161-5949-8a47-676a0ba9f986&ClickID=2'
    headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.2 Safari/605.1.15'
    }
    page_text = requests.get(url = url, headers = headers).text

    # xpath数据解析
    tree = etree.HTML(page_text)
    div_list = tree.xpath('//section[@class="list"]/div')
    
    fp = open('./ershoufang.txt','w',encoding='utf-8')
    for div in div_list:
        title = div.xpath('./a//div[@class="property-content-title"]/h3/text()')[0]
        print(title)
        fp.write(title + '\n')

```
### xpath案例-
