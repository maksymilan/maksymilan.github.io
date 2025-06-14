---
title: python爬虫
date: 2024-08-08 15:59:52
updated: "08 Aug 2024"
publishDate: "08 Aug 2024"
description: "这篇文章介绍了Python爬虫的基本概念和常用库，包括urllib、requests、beautifulsoup和selenium等，帮助读者快速上手Python爬虫开发。"
tags: ["python", "爬虫", "web scraping", "requests", "beautifulsoup", "selenium"]
---

# Urllib

Urllib是Python的内置库，包含以下四个模块

`request`:发起请求

`error`:在request模块中遇到错误时进行异常处理

`parse`:解析URL地址

`robotparser`:解析网站的robot.txt

### 模拟访问网页

我们用urlopen方法用Get请求方式访问百度

```python
import urllib.request
response = urllib.request.urlopen('http://www.baidu.com')
print(response.read().decode('utf-8'))
```

`urlopen`可以传入的参数有三个`urllib.request.urlopen`(url, data=None, [timeout, ])

url是我们的请求链接，data是在post请求中携带参数的，timeout是设置请求超时时间

#### 添加请求头信息

当我们要模拟浏览器访问的时候，我们需要添加请求头信息，使用request模块的Request方法

`urllib.request.Request`(*url*, *data=None*, *headers={}*, *method=None*)

urlopen 默认是 Get 请求，当我们传入参数它就为 Post 请求了，而 Request 可以让我们自己定义请求的方式

data是post请求携带的信息，一般是密码账号之类

# requests

requests库的函数更加简单

`get`:r = requests.get('https://api.github.com/events')

`post`:r = requests.post('https://httpbin.org/post', data = {'key':'value'})

伪装成浏览器

```powershell
>>> url = 'https://api.github.com/some/endpoint'

>>> headers = {'user-agent': 'my-app/0.0.1'}

>>> r = requests.get(url, headers=headers)
```

具体内容见这篇[文档](https://requests.readthedocs.io/projects/cn/zh-cn/latest/)

# beautifulsoup

beautifulsoup是一个网页解析库，可以将我们得到的网页源码进行解析，过滤出自己想要的内容

## selenium