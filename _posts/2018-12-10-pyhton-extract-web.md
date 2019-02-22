---
layout: post
title: Python提取网站数据基本教程
subtitle: Using urllib, beautifulsoup4
author: "WangW"
header-style: text
tags: 爬虫
---

-----

### 0. 目录

- [0. 目录](#0-目录)
- [1. 解析网址例子](#1-解析网址例子)
- [2. 提取网页数据](#2-提取网页数据)
- [3. 使用正则表达在网页中提取所想要的内容](#3-使用正则表达在网页中提取所想要的内容)
- [4. 网页分析与应用](#4-网页分析与应用)
- [5. 后记](#5-后记)

<!--break-->

### 1. 解析网址例子

使用urllib.parse模块来解析网址
```python
from urllib.parse import urlparse
uc = urlparse('http://blog.sina.com.cn/s/blog_4e345ca90102wm9v.html?tj=fina')
print(uc)
print(uc.netloc)
# result
# ParseResult(scheme='http', netloc='blog.sina.com.cn', path='/s/blog_4e345ca90102wm9v.html', params='', query='tj=fina', fragment='')
# blog.sina.com.cn
```

```python
from urllib.parse import urlparse

ur1 = 'http://xa.58.com/hezu/33234645063725x.shtml?psid=157273217199223692452914449&ClickID=2&cookie=||https://www.google.com/|c5/njVqYsK5HO3MUB9jaAg==&PGTID=0d3090a7-001e-3db6-cece-a8d9e668e348&apptype=0&entinfo=33234645063725_0&fzbref=0&iuType=gz_2&key=&pubid=28056322&from=1-list-0&params=busitime^desc&local=483&trackkey=33234645063725_512fc4d7-75f8-4285-a8d2-25cb5d61d4d1_20180302100235_1519956155555&fcinfotype=gz'

uc = urlparse(ur1)

print("NetLoc: ", uc.netloc)
print("Path: ", uc.path)

q_cmds = uc.query.split('&')
print("Query Commands: ")
for q_cmd in q_cmds:
    print(q_cmd)
# result
# NetLoc:  xa.58.com
# Path:  /hezu/33234645063725x.shtml
# Query Commands: 
#psid=157273217199223692452914449
#ClickID=2
#.......
```
从上面的例子可以看出来，改变请求（query）等号后面的数字，就会到不同的页面。
***
### 2. 提取网页数据
在练习中，使用 requests 模块, 如果没有安装这个模块，在cmd命令行直接 pip install requests 就可以了。

```python
from urllib.parse import urlparse
import requests

ur1 = 'http://xa.58.com/hezu/33234645063725x.shtml?psid=157273217199223692452914449&ClickID=2&cookie=||https://www.google.com/|c5/njVqYsK5HO3MUB9jaAg==&PGTID=0d3090a7-001e-3db6-cece-a8d9e668e348&apptype=0&entinfo=33234645063725_0&fzbref=0&iuType=gz_2&key=&pubid=28056322&from=1-list-0&params=busitime^desc&local=483&trackkey=33234645063725_512fc4d7-75f8-4285-a8d2-25cb5d61d4d1_20180302100235_1519956155555&fcinfotype=gz'

uc = urlparse(ur1)
htm1 = requests.get(ur1).text.splitlines()#使用requests.get提取网页内容，并以文本的格式存放到htm1中
for i in range (0, 15):
    print(htm1[i])

# result
# <!DOCTYPE html>
# <html>
# <head>
#   <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
#   <title>
#       【7图】(单间出租)土门 任家口 开远门地铁口 西电医院 大庆路
#       丰禾路太奥广场,沣惠北路42号-西安58同城    </title>
```
***
### 3. 使用正则表达在网页中提取所想要的内容

![](https://user-gold-cdn.xitu.io/2018/3/2/161e4dbdd3866120?w=1372&h=941&f=jpeg&s=713518)

```python
from urllib.parse import urlparse
import requests
import re

regax = r"([a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+)" #邮箱账号正则表达
ur1 = "http://blog.sina.com.cn/s/blog_147f99d5d0102vmyx.html"#仅做测试用


htm1 = requests.get(ur1).text
emails = re.findall(regax, htm1)

for eamil in emails :
    print(eamil)

```
***
### 4. 网页分析与应用
在这次的练习中使用pip install beautifulsoup4 模块。

一般的操作是：用request模块提取网页数据，将提取的数据转换成文本之后存放在html中，然后把htm1使用beautifulsoup加以解析，解析之后放在sp中，之后就可以用beautifulsoup所提供的函数存取sp中解析好的数据了，这些函数主要是通过标签来操作的。

![](https://user-gold-cdn.xitu.io/2018/3/2/161e4db53c01bde1?w=1386&h=843&f=jpeg&s=749499)

```python
from urllib.parse import urlparse
import requests
import re
from bs4 import BeautifulSoup as bs

ur1 = "http://blog.sina.com.cn/s/blog_147f99d5d0102vmyx.html"

htm1 = requests.get(ur1).text
sp = bs(htm1,'lxml')

#emails = re.findall(regax, htm1)
#for eamil in emails :
#    print(eamil)

links = sp.find_all('a')
a = links[1] #打印第二个链接
print(a)
```
使用beautifulsoup提取信息

1. title:返回此页面的标题-sp.title
2. text:除去所有HTML标签，把网页变为字符串返回-so.text
3. find：返回第一个符合条件的内容-sp.find('img')
4. find_all:返回所有符合条件的内容-sp.find_all('a')
5. select:返回以CSS选择器作为运算结果的所有内容，主要操作对象为id和class-sp.select('#Showtd')

```python
import requests
import sys
from bs4 import BeautifulSoup as bs

if len(sys.argv) < 2:
    print("用法：python **.py <target url>") #如果输入的网址少于2就报错
    exit(1)

ur1 = sys.argv[1]

htm1 = requests.get(ur1).text
sp = bs(htm1,'lxml')
all_links = sp.find_all('a')


for link in all_links :
    href = link.get('href')
    if href != None and href.startswith('http://'):
        print(href)
#需要用到cmd 输入指令.py http://www.baidu.com
```
用同样的方法提取网页中所有的图像文件链接

```python
import requests
import sys
from bs4 import BeautifulSoup as bs
from urllib.parse import urlparse

if len(sys.argv) < 2:
    print("用法：python **.py <target url>") #如果输入的网址少于2就报错
    exit(1)

url = sys.argv[1]
domain = "{}://{}".format(urlparse(url).scheme, ur1parse(url).hostname)
htm1 = requests.get(url).text
sp = bs(htm1,'lxml')
all_links = sp.find_all('a', 'img')


for link in all_links :
    src = link.get('src')
    href = link.get('href')
    targets = [src, href]
    for t in targets:
        if t.startswith('http'): full_path = t
        else:                    full_path = domain
        print(full_path)
        if not os.path.exists(img_dir):os.mkdir(img_dir)
        filename = full_path.split('/')[-1]
        ext = filename.split(".")[-1]
        filename = filename.split('.')[-2]
        if 'jpg' in ext : filename = filename + '.jpg'
        else :            filename = filename + '.png'
        image = urlopen(full_path)
        fp = open(os.patn.join(image_dir, filename), wb)
        fp.write(image.read())
        fp.close()
```
-----

### 5. 后记

本文主要罗列了在爬虫中常用的python库。

要学好爬虫，个人认为最重要的是HTLL、css等概念。

------

**TQ**
