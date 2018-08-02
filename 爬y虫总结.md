##  爬y虫总结:

### 使用urllib发送get方法：

##### 解决ssl报错 问题：

```
import ssl
ssl._create_default_https_context = ssl._create_unverified_context
```

```python
#例：
url = 'https://movie.douban.com/j/search_subjects?type=tv&tag=%E7%83%AD%E9%97%A8&'
page = input()
data = {...}
# 转化data
data = urllib.parse.urlencode(data)
url = url +data
response = urllib.request.urlopen(url)
response.read().decode('utf-8')
```

#### urlretrieve :下载资源

```python
import urllib.request
url = 'http://.......'
response = urllib.request.urlretrieve(url = url,filename = 'xx')
```

### post方法应用：

```python
url = '....'
headers = {......}
#构造post请求的data
data = {
    'key':value
}
data = parse.urlencode(data).encode('utf-8')
req = request.Request(url=url,headers = headers ,data=data)
response = request.urlopen(req)
print(response.read().decode('utf-8'))
```

#### urlib.parse/urllib.request

```python
import urllib.request
import urllib.parse


url = 'http://fanyi.baidu.com/sug'

word = input('请输入您要查找的英文单词：')
# 构造post请求的data。
data = {
    'kw': word,
}

data = urllib.parse.urlencode(data).encode('utf-8')

response = urllib.request.urlopen(url=url, data=data)

print(response.read().decode('utf-8'))
```



### 使用urllib发送Post方法：

在使用urlopen时，指定data参数，就表示使用post进行访问。

```python
import urllib import request
import urllib import parse
url = 'http://xxx'
data = {
    'kw':word
}
# 把字典转换成关键词参数的形式, 同时指定编码方式
data = parse.urlencode(data).encode('utf-8')

 # 带上了data数据表明这是个post请求
response = request.urlopen(url=url,data=data)

# 
with open('./post.txt','w',encoding='utf-8') as fp:
    fp.write(response.read().decode('utf-8'))
```

```python
import urllib.request
import urllib.parse

# 百度手机翻译
url = 'http://fanyi.baidu.com/basetrans'

# word = input('请输入您要查找的英文单词：')
# 构造post请求的data。
# data = {
#     'from': 'en',
#     'to': 'zh',
#     'query': 'love',
#     'transtype':'realtime',
#     'simple_means_flag':'3',
#     'sign':'954346.683227',
#     'token':'dcc146aee5efd9f2d03d4b4dd06a2ec5'
# }
"""
sign:284353.46576
token:dcc146aee5efd9f2d03d4b4dd06a2ec5
"""
data = {
    'from': 'en',
    'to': 'zh',
    'query': 'love',
}

headers = {
# 手机浏览器的UA
    "User-Agent":"Mozilla/5.0 (Linux; Android 5.1.1; Nexus 6 Build/LYZ28E) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Mobile Safari/537.36"
}

#加了头部信息要Request 不能直接open 
data = urllib.parse.urlencode(data).encode('utf-8')

req = urllib.request.Request(url=url, data=data, headers=headers)
response = urllib.request.urlopen(req)

print(response.read().decode('utf-8'))


```



### ajax:get请求：

分析豆瓣电影排行榜发现，排行榜没有分页，只要往下拉，就能自动刷出新的电影来，这是典型的ajax请求。

分析发现豆瓣电影通过ajax的get请求实现了这一功能。

```python
from urllib import request,parse
import ssl
ssl._create_default_https_context = ssl._create_unverified_contexturl = url = 'https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&'
page = int(input('请输入您想要第几页电影：'))

data = {    'start': (page -1) * 20,    'limit': '20'}
# 将data拼接为get请求的查询字符串
data = parse.urlencode(data)
url = url + data
headers = {....}
req = request.Request(url=url,headers =headers)
response = request.urlopen(req)
# 爬取的页面写入文档
with open('./get.txt','w',encoding='utf-8') as fp
	fp.writr(response.read().encode('utf-8'))
```

```python
import urllib.request
import urllib.parse
import ssl

# 解决ssl证书问题
ssl._create_default_https_context = ssl._create_unverified_context


url = 'https://movie.douban.com/j/chart/top_list?type=5&interval_id=100%3A90&action=&'

# 输入页码
page = int(input('请输入您想获取的页码：'))
# start=0&limit=20
data = {
    'start': (page-1) * 20,
    'limit': 20,
}

data = urllib.parse.urlencode(data)

# url 拼接
url = url + data

# 创建request对象
request = urllib.request.Request(url=url)
response = urllib.request.urlopen(request)

print(response.read().decode('utf-8'))


```



### ajax：Post请求：

```python
 from urllib import request,parse

url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname'

data = {'cname': '深圳','pid': '','pageIndex':'2','pageSize': '10'}

# post 请求注意转码
data = parse.urlencode(data).encode('utf-8')

response = request.urlopen(url=url,data=data)

response.read().decode('utf-8')

```

```python
import urllib.request
import urllib.parse


url = 'http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname'

data = {
    'cname': '深圳',
    'pid': '',
    'pageIndex': '5',
    'pageSize': '10'
}

data = urllib.parse.urlencode(data).encode('utf-8')

# 创建请求对象
request = urllib.request.Request(url=url, data=data)

response = urllib.request.urlopen(request)

print(response.read().decode('utf-8'))

```



### URLError 和 HTTPError

HTTPError类是URLError 的子类

通过urllib发送请求的时候，有可能会发送失败，这个时候如果想让你的代码更加的健壮，可以通过try-except进行捕获异常，异常有两类，URLError\HTTPError。

为了让我们的代码更加健壮，我们可以尝试捕获可能出现的异常，比如：

```python
import urllib.request,urllib.parse,url.error

url = '...'
try:
    response = urllib.request.urlopen(url)
    print(response.read().decode('utf-8'))
#小范围异常写前面 
except urllib.error.HTTPError as e:
    print(e)
#大范围异常写在后面
except urllib.error.URLError as e:
    print(e)
```

### Handler处理器，自定义Opener

（1）前面学习的urllib.request.urlopen()很简单的一个获取网页的函数，但是它不能自己构建请求头
（2）引入了request对象，request = urllib.request.Request(url=url, headers=headers),它的高级之处就是可以自己定制请求头
（3）request对象不能携带cookie，也不能使用代理，所以引入了Handler处理器、自定义Opener

```python
import urllib.request

# 首先创建一个handler对象
handler = urllib.request.HTTPHeadler()

#创建opener对象
opener = urllib.request.build_opener(handler)

# 构建请求对象
url = 'http://www.baidu.com/'
headers = {    
'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36'
}

#创建request 对象
request = urllib.request.Request(url=url headers=headers)

#使用opener打开request
response = opener.open(reqeust)

print(response.read().decode('utf-8'))


```

### 使用代理 handler：

```python
import urllib.requst
# 创建代理handler
handler = urllib.request.ProxyHandler({'http':'10.36.137.2'})

#创建 opener
opener = urllib.request.build_opener(handler)

url = 'https://www...'
req = urllib.request.Request(url=url)

response = opener.open(req)

print(response.read().decode('utf-8'))

```

### cookjar 的使用：

通过cookiejar库，创建一个handler，然后根据这个handler创建一个opener，然后通过这个opener去登录你的微博，然后再opener去访问登录后的页面即可，这个handler会自动的保存登录之后的cookie

```python
import urllib.request
import http.cookiejar
import urllib.parse

#创建cookie对象
cookie = http.cookiejar.CookieJar()
#c创建handler
handler = urllib.request.HTTPCookieProcessor(cookie)
# 创建opener
opener = urllib.request.build_opener(handler)


data = {
    'username': '18676689715',
    'password': 'xuke666',
    'savestate': '1',
    'r': 'http://weibo.cn/',
    'ec': '0',
    'pagerefer': '',
    'entry': 'mweibo',
    'wentry': '',
    'loginfrom': '',
    'client_id': '',
    'code': '',
    'qq': '',
    'mainpageflag': '1',
    'hff': '',
    'hfp': ''
}

data = urllib.parse.urlencode(data).encode('utf-8')
headers = {
    'Host': 'passport.weibo.cn',
    'Connection': 'keep-alive',
    'Origin': 'https://passport.weibo.cn',
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Accept': '*/*',
    'Referer': 'https://passport.weibo.cn/signin/login?entry=mweibo&r=http%3A%2F%2Fweibo.cn%2F&backTitle=%CE%A2%B2%A9&vt=',
    'Accept-Language': 'zh-CN,zh;q=0.8',
}
request = urllib.request.Request(url='https://passport.weibo.cn/sso/login', headers=headers, data=data)
# 登录页面 保存cookie
response = opener.open(request)
print(response.read().decode('utf-8'))
#<-------------------------------------------------------------->
headers = {
'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36',
}

# 访问个人详细信息 cookie已经保存在里面
request = urllib.request.Request(url='https://weibo.cn/2952685222/info', headers=headers)

# 使用opener访问
response = opener.open(request)

print(response.read().decode('utf-8'))


```

### 函数封装抓取贴吧：

```python
import urllib.request
import urllib.parse
import ssl
import os

# 解决ssl证书问题
ssl._create_default_https_context = ssl._create_unverified_context



def handle_url(base_url, page, tieba_name):
    # 生成url
    data = {
        'kw': tieba_name,
        'pn': (page -1) * 50,
    }
    data = urllib.parse.urlencode(data)
    url = base_url + data
    # 生成request对象
    request = urllib.request.Request(url=url)
    # 返回
    return request


def download(request, page, tieba_name):
    response = urllib.request.urlopen(request)
    # 拼保存的文件名
    filename = tieba_name + str(page) + '.html' # python1.html python2.html
    with open(os.path.join('./tieba', filename), 'w', encoding='utf-8') as fb:
        fb.write(response.read().decode('utf-8'))


def main():
    base_url = 'http://tieba.baidu.com/f?ie=utf-8&'
    tieba_name = input('请输入想爬取的贴吧名：')
    start_page = int(input('请输入起始页：'))
    end_page = int(input('请输入结束页：'))


    # 循环处理每一页
    for page in range(start_page, end_page+1):
        # 处理url
        request = handle_url(base_url, page, tieba_name)
        # 下载页面
        # 封装函数执行下载动作
        print('开始下载第%d页'%page)
        download(request, page, tieba_name)
        print('结束下载第%d页' % page)


if __name__ == '__main__':
    main()
```



### Xpath的使用：

```python
	//  从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置
	.   选取当前节点
	@   选取属性
打开谷歌浏览器，安装xpath插件，然后使用xpath插件
按 ctrl + shift + x

属性定位：根据属性查找标签
层级定位：一级一级查找
索引定位：【注】下标从1开始
	查找id是maincontent的div下面的h1节点
	//div[@id="maincontent"]/h1

	//div[@class="head_wrapper"]/div[@id="u"]/a[1]
逻辑运算
	//div[@id="head" and @class="s_down"]
模糊匹配
	查找所有的div，id中有he的div
	//div[contains(@id, "he")]
	查找所有的div，id中以he开头的div
	//div[starts-with(@id, "he")]
	查找所有的div，id中以he结尾的div
	//div[ends-with(@id, "he")]
取文本
	//div[@class="head_wrapper"]/div[@id="u"]/a[1]/text()
	//div[@class="head_wrapper"]/div[@id="u"]/a[1]
	obj.text   将内容获取到
取属性
	//div[@class="head_wrapper"]/div[@id="u"]/a[1]/@href
```

安装模块  lxml库
	这个库是解析html的库，主要就是解析和提取数据
	pip install lxml  安装好
	【注】pip安装包的时候，要记得将fiddler关闭
代码中使用
	from lxml import etree
	html_etree = etree.parse('可以获取本地html文件')     
	html_etree = etree.HTML('网上获取的html字符串(也可以是字节类型)')
	html_etree.xpath('xpath路径')
	返回的是一个列表

​	获取到节点对象之后obj
		obj.xpath('xpath路径')



```python
from lxml import etree
import urllib.request
import ssl
ssl._create_default_https_context = ssl._create_unverified_context
str = """
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html"><span class="bold">third item</span></a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a></li>
     </ul>
 </div>
"""

html_etree = etree.fromstring(str)  
# response = urllib.request.urlopen(url='http://www.baidu.com')

html_etree = etree.HTML(str)
print(etree.tostring(html_etree).decode('utf-8'))

# 查找本地的html
html_parse = etree.parse('./test.html')
print(etree.tostring(html_parse).decode('utf-8'))

li_list = html_etree.xpath('//li')
print(li_list)
#
# #1. 查找所有的li
li_list = html_etree.xpath('/html/body/div/ul/li')
print(li_list)

# 2. 获取所有class属性
# 所有属性
c_list = html_etree.xpath('//*/@*')
print(c_list)
c_list = html_etree.xpath('//*/@class')
print(c_list)


# 3 获取所有li下的span标签
span_list = html_etree.xpath('//li//span')
print(span_list)          #[<Element span at 0x292c308>]
print(span_list[0].text)  #third item
#

# # 4 获取倒数第二个li
li = html_etree.xpath('//li[last()-1]')
# print(li)
print(etree.tostring(li[0]).decode('utf-8'))
#<li class="item-1"><a href="link4.html">fourth item</a></li>
# 选择li里面的a
a = li[0].xpath('./a')
print(a)
#永远记得xpath返回的是一个列表。
# print(etree.tostring(a[0])) #b'<a href="link4.html">fourth item</a>'

# 查询li并且class等于item-0
li = html_etree.xpath('//li[@class!="item-0"]')
print(li)
print(etree.tostring(li[0]).decode('utf-8'))
#<li class="item-1"><a href="link2.html">second item</a></li>


# 属性中包含inactive

li = html_etree.xpath('//li[contains(@class, "inactive")]')
print(li)
print(etree.tostring(li[0]).decode('utf-8'))
#<li class="item-inactive"><a href="link3.html"><span class="bold">third item</span></a></li>

# 不包含
li = html_etree.xpath('//li[not(contains(@class, "inactive"))]')
print(etree.tostring(li[0]).decode('utf-8'))
# <li class="item-0"><a href="link1.html">first item</a></li>


# # 所有内容包含item的元素
el = html_etree.xpath('//*[contains(text(), "item")]')
print(el)
```

### 正则爬取西刺代理：

```python

# from urllib import request,parse
import urllib.request
import urllib.parse
import re


def get_html(req):
    response = urllib.request.urlopen(req)
    res_html = response.read().decode('utf-8')
    pattern = re.compile(r'<tr class="odd">(.*?)</tr>',re.S)
    ip_list = pattern.findall(res_html)
    # print(ip_list)
    for item in ip_list:
        ip_patterns = re.compile('(25[0-5]\.|2[0-4]\d\.|1\d{2}\.|[1-9]\d\.|\d\.){3}(25[0-5]|2[0-4]\d|1\d{2}|[1-9]\d|\d)',re.S)

        ip_data = ip_patterns.search(item)

        host_patterns = re.compile(r'(\d{2,6})')
        host_data = host_patterns.search(item)
        # print(ip_data)
        print('ip地址为:%s 端口为:%s' % (ip_data.group(),host_data.group()))




def main():
    url = 'http://www.xicidaili.com/nn/'

    headers = {
        'Accept':' */*',
        'Accept-Language':' zh-CN,zh;q=0.9',
        'Connection':' keep-alive',
        'Host':' hm.baidu.com',
        'If-None-Match':' 5c3b05459187e67c077312cdbbe0a08c',
        'Referer':' http://www.xicidaili.com/nn/',
        'User-Agent':' Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.181 Safari/537.36',
        'Cookie':' BAIDUID=A56FDCF3F2FFF6E6848D0703341886EC:FG=1; BIDUPSID=A56FDCF3F2FFF6E6848D0703341886EC; PSTM=1525660571; HMACCOUNT=BBB3CB5E8BE6A3F6; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; H_PS_PSSID=1437_21121_26350_20930; PSINO=7; HMVT=6bcd52f51e9b3dce32bec4a3997715ac|1528005508|; BCLID=11740774069474897488; BDSFRCVID=S64sJeC62wWhRX57LkmAhMndP4fN8Q6TH6aI5UoouSi9z5W9oqRYEG0PDM8g0KubbazpogKK3gOTH4nP; H_BDCLCKID_SF=tJPfoCtMfIP3fP36qRrhh4At-fT0bI62aKDsLJ7I-hcqEIL45pJs0lv0jPvjLJO3BCobbIbDBDnGMxbSj4QojqLehpoga6cEJGk8W-j4yl5nhMJeXj7JDMP0-x5KhJOy523iob3vQpPMDxtuD68We5v-DNDs-bbfHDr0Lnbq2RREKROvhjRP3-PyyxomtjjH5HRAbJ_5BPtW8bnEh4OmhJF_D-PtLUkqKCO3_n7MJDozs4JJXJ5t34uRQttjQU3ufIkja-5tKt3GqJ7TyU45bU47yaOXQTIHJnkDoItyfIvbfP0k2Jo_h-F_hxrjetJyaR3e2PbbWJ5TMCo63P7C-j5WXMRfK4vuaDnTbquhQpC-ShPC-tPh3t4e5Jb0qRItQb5yQxow3l02VhbEe-t2ynLV-xQEbPRMW20e0h7mWIb_VKF4j5taD5ObeaRf-b-XM6TXWnTqaJT2KROvhjRzQnLyyxomtjjfQKbN3CL5BnvbbJnEh4Om0x6L3-FqLUkqKC8t-R7ObxOEbtFG3TroKqD4QttjQU3ufIkja-5tKJj0qb7TyU45bU47yaOXQTIHJnkDoItyfIvbfP0k2Jo_h-F_hxrjetJyaR0J_qQbWJ5TMCo63P7CW5QWXM4JtPvuaDnT_nQkfUD-ShPC-fuh3t4e5Jb0qRItQb5yQxow3l02VhbEe-t2ynLV-xQEbPRMW20e0h7mWIb_VKF4D60WejoQjaRf-b-X5I6J3C5aHJOoDDvFLTO5y4LdjG5C5J3BHC_f2nv4WJnYDRbM5UQDDbKX3-Aq5xcUQJ6LQn5Ibt5AJbcChhoDQfbQ0hQuqP-jW5TahnjEbb7JOpkRbUnxy50H0aCHtT-Dtn4OVIv5b-0_Jt5kh-rHh4FQqxby26ngJJneaJ5nJDobSnn-W5_bX-PDLf7u2t3TJIcwVUDKQpP-HqIG3tno5qtteqCtL5cy-GrkKl0MLpbYbb0xynoD-4_hKMnMBMPe52OnaIb8LIcjqR8Zj60MD6QP',

    }

    start_page = int(input('请输入你想爬取的起始页面:'))
    end_page = int(input('请输入你想爬取的结束页面:'))

    for page in range(start_page, end_page+1):
        print("正在爬取第%s页......"%(page))
        url = url + str(page)
        req = urllib.request.Request(url = url, headers = headers)
        get_html(req)
        print('结束第%s页!'%(page))


if __name__ == '__main__':
    main()
    
```

### 正则的使用：

```python
import re

str = ',039794795abcfjeiowf,0238403895gjoirlkdn  f2f23prhg9g83'

pattern = re.compile(r'\w+')

#1 使用match
m = pattern.match(str)
# print(m)
# # 取出匹配到的字符串
# print(m.group())
# # 范围
# print(m.span())
# # 起始索引
# print(m.start())
# print(m.end())

# 2. search
p = re.compile(r'\d+')
p = re.compile((r'\,(\d+)[a-z]+'))
result = p.search(str)
print(result)
print(result.group())


#. findall 找出所有， 返回列表
num_list = p.findall(str)
print(num_list)


# finditer 返回可迭代对象。
result = p.finditer(str)
print(result)
for item in result:
    print(item.group())


# split 根据正则中的字符来分割字符串
str = 'aa,bb,cc : :    b  e : t '
p = re.compile(r'[\,\s\:]')
sub_list = p.split(str)
print(sub_list)

# sub 替换
str = '123 Hello, 456 World'
p = re.compile(r'(\d+) (\w+)')
print(p.findall(str))
print(p.sub(r'\2 \1', str))
print(p.sub(r'\2 zhangsan', str))
def fun(item):
    return 'cool ' + item.group(2)
print(p.sub(fun, str))


# 匹配汉字
str = '你好everyone，你好我好 大家好，good'
p = re.compile(u'[\u4e00-\u9fa5]+')
print(p.findall(str))


# 贪婪模式和非贪婪模式
str = 'aa<div>test1</div>bb<div>test2</div>cc'

# 非贪婪模式
p  = re.compile(r'<div>(.*?)</div>')
print(p.findall(str))
print(p.search(str))

# 贪婪模式
p  = re.compile(r'<div>(.*)</div>')
print(p.findall(str))
print(p.search(str))

# ip地址 ，使用正则表达式如何匹配。

```

### requests 的使用：

Requests 唯一的一个**非转基因**的 Python HTTP 库，人类可以安全享用。 

官方文档：http://cn.python-requests.org/zh_CN/latest/

安装：pip install requests -i https://pypi.douban.com/simple

```python
import requests


url = 'https://www.baidu.com'
#
r = requests.get(url=url)
# # 查看内容
print(r.text)
#
# # 查看编码
print(r.encoding)
#
# # 转换编码
r.encoding = 'utf-8'
#
print(r.text)

# get请求带参数
r = requests.get(url=url)
r.encoding = 'utf-8'

# 查看请求地址
print(r.url)

print(r.content)
# 状态码
print(r.status_code)

# 头部信息
print(r.headers)
# print(r.text)
with open('./baidu58.html', 'w', encoding='utf-8') as fp:
     fp.write(r.text)

```

### requests-post:

```python
import requests
url = 'http://fanyi.baidu.com/sug'

data = {
    'kw': 'hello'
}

r = requests.post(url=url, data=data)

print(r.text)

# 把json数据编码
print(r.json())


```

### requests:代理

```python
import requests

url = 'http://www.baidu.com/s'

data = {
    'wd': 'ip'
}

proxies = {
    'http': '115.153.173.238:61234'
}

r = requests.get(url=url, params=data, proxies=proxies)
r.encoding = 'utf-8'

with open('./baiduip.html', 'w', encoding='utf-8') as fp:
    fp.write(r.text)
```

### requests:session 

requests中使用session跟踪会话，session会自动记录cookie，相当于urllib和cookiejar结合。

在requests中使用session跟踪会话非常简单：

```python
import requests 

post_url = 'https://passport.weibo.cn/sso/login'
data = {
    'username': '18676689715',
    'password': 'xuke666',
    'savestate': '1',
    'r': 'http://weibo.cn/',
    'ec': '0',
    'pagerefer': '',
    'entry': 'mweibo',
    'wentry': '',
    'loginfrom': '',
    'client_id': '',
    'code': '',
    'qq': '',
    'mainpageflag': '1',
    'hff': '',
    'hfp': '',
}
headers = {
    'Host': 'passport.weibo.cn',
    'Connection': 'keep-alive',
    'Origin': 'https://passport.weibo.cn',
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Accept': '*/*',
    'Referer': 'https://passport.weibo.cn/signin/login?entry=mweibo&r=http%3A%2F%2Fweibo.cn%2F&backTitle=%CE%A2%B2%A9&vt=',
    'Accept-Language': 'zh-CN,zh;q=0.9',
}
# 创建session 对象 session自动记录cookie
session = request.Sesion()

r = session.post(url=post_url,data=data,headers=headers)
print(r.json())

url = 'https://weibo.cn/2952685222/info'
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36',
}

# 访问个人信息页面
r = session.get(url=url, headers=headers)
with open('./weibo.html', 'w', encoding='utf-8') as fp:
    fp.write(r.text)


```



### json：

```python
# 从json 文件中创建对象
obj = json.load(open('./book,json','r',encoding='utf-8'))
print(obj)

#使jump把字符串写入文件中
dictStr = {"city": "北京", "name": "大刘",'info':'\u8c22\u8c22\u5927'}
#
json.dump(dictStr, open('dump.json', 'w', encoding='utf-8'), ensure_ascii=False)


#从字符串中创建python对象：
with open('./book,json','r',encoding='utf-8')
	json_string = fp.read()
 obj = josn.loads(json_string)
print(obj)

#将字符串变成json对象写入
str = '''
  {"has_more": false, "message": "success", "data": [{"single_mode": true, "abstract": "\u8c22\u8c22\u5927\u5bb6\u559c\u6b22\u6bcf\u65e5\u64b8"}]}
 '''
obj = json.dumps(str, ensure_ascii=False)
print(type(obj), obj)
#
```



### JsonPath的使用：

![1528619403919](C:\Users\pc\AppData\Local\Temp\1528619403919.png)

book.json:

```python
{ "store": {
    "book": [ 
      { "category": "reference",
        "author": "李白",
        "title": "Sayings of the Century",
        "price": 8.95
      },
      { "category": "fiction",
        "author": "杜甫",
        "title": "Sword of Honour",
        "price": 12.99
      },
      { "category": "fiction",
        "author": "白居易",
        "title": "Moby Dick",
        "isbn": "0-553-21311-3",
        "price": 8.99
      },
      { "category": "fiction",
        "author": "苏轼",
        "title": "The Lord of the Rings",
        "isbn": "0-395-19395-8",
        "price": 22.99
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95
    }
  }
}
```

#### jsonpath的使用方法：

```python
import jsonpath
import json

# 获取json格式的python对象
obj = json.load(open('./book.json', 'r', encoding='utf-8'))

authors = jsonpath.jsonpath(obj, '$..book[*].author')
print(authors)
#['李白', '杜甫', '白居易', '苏轼']
#
authors = jsonpath.jsonpath(obj, '$..author')
print(authors)
#['李白', '杜甫', '白居易', '苏轼']

prices = jsonpath.jsonpath(obj, '$.store..price')
print(prices)
#[8.95, 12.99, 8.99, 22.99, 19.95]

# 获取第二本书以后的价格
last_book = jsonpath.jsonpath(obj,'$.store.book[2:].price')
print(last_book)

# 取最后一本书
last_book = jsonpath.jsonpath(obj, '$..book[(@.length-1)]')
print(last_book)
#[{'category': 'fiction', 'author': '苏轼', 'title': 'The Lord of the Rings', 'isbn': '0-395-19395-8', 'price': 22.99}]

last_book = jsonpath.jsonpath(obj, '$..book[:2]')
print(last_book)
#[{'category': 'reference', 'author': '李白', 'title': 'Sayings of the Century', 'price': 8.95}, {'category': 'fiction', 'author': '杜甫', 'title': 'Sword of Honour', 'price': 12.99}]


```



### 1,线程锁和队列锁：

```python
import queue
import threading
import time

# 设置一个线程退出的标志
exit_flag = False
q = queue.Queue(10)

class myThread(threading.Thread)
	def __int__(self,q,*args,**kwargs):
    	super().__init__(*args,**kwargs)
		self.q =q
        
        def run(self):
            # 无序循环取数据
			while True:
                if exit_flag:
                    break
                #取数据
                value = self.q.get() # 默认为True
                
                # 告诉q 取数据完成。 q的join方法
                # 也是判断队列是否为空 
                self.q.task_done() # 执行第一次  在内部计数q - 1
                print('%d数据取完了'%value)
                
 #get(self, block=True, timeout=None)
#block为True，若队列为空，并且timeout为正值，该方法会阻塞timeout指定的时间，直到队列#中有出现新的数据，如果超时，会抛出Queue.Empty异常 
#block为False，若队列为空，立即抛出Queue.Empty异常
#put(self, obj, block=True, timeout=None)
#1、block为True，若队列已满，并且timeout为正值，该方法会阻塞timeout指定的时间，直到#队列中有出现剩余空间，如果超时，会抛出Queue.Full异常
#2、block为False，若队列已满，立即抛出Queue.Full异常   
def main():
    print('主线程开始')
    # 往队列中存放数据
    for i in range(10):
        q.put(i)
    
    #创建线程
    t = myThread(q)
    t.start()
    #取数据
    #在线程中取数据
    # 线程的join函数即为线程锁
    # t.join()  # 线程锁依赖线程  需要等线程执行完毕才能执行
    # 线程需要退出标志改为false 才执行退出 两者互相等待
	
    # 所以使用队列锁,当q 为空的时候 才会继续执行
    q.join()
    #设置 线程停止标志
    global exit_flag
    exit_flag = True
    print('线程结束')


```

### 多线程爬虫：

```python
form urllib import URLError 

import requests
import threading
import time
import queue
import random
from lxml import etree
import json

#创建队列
# 1 创建spider队列
spider_queue = queue.Queue()

# 2 创建解析对列
parse_queue = queue.Queue()
#解析线程退出的标志
parse_exit_flag = False

#写入文件防止错乱 加锁
lock = threading.Lock()

UserAgent_List = [
        "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36",
]

# 创建spider类
class SpiderThread(threading.Thread):
    # s_id:spider的id 
    def __init__(self,s_id,s_queue,p_queue,*args,**kwargs) 
	self.id = s_id
    self.spider_queue= s_queue
    self.parse_queue = p_queue

    def run(self):
        while True:
            time.sleep(3)
            #退出条件
            if self.spider.empty():
                break
            # 取数据   
            #block为False，若队列为空，立即抛出Queue.Empty异常
            url = self.spider_queue.get(block=False)
            
            # 使用requests 获取数据
            try:
        		response = reqeusts.get(url=url,headers={'User-Agent':random.choice(UserAgent_List)})        
        		#将数保存put到parse_queue中
                self.parse_queue.put(response.text)
                print(%d线程爬取数据%s % (self.id,url))
            except URLError as e:
                print(e,'网络请求错误')
            finally：
            	self.spider_queue.task_done()
            
            

 #创建解析类
class ParseThread(threading.Thread):
    def __init__(self,p_id,p_queue.fq,*args,**kwargs):
        super().__init__(*args,**kwargs)
        self.id = p_id
        self.parse_queue = p_queue
        self.fq = fq
        
        def run(self):
            global parse_exit_flag
            if parse_exit_flag:
                break
            try:
                data = self.parse_queue.get(block=False)
                self.parse(data)
                print('%d线程解析数据成功'%self.id)
                # 解析完成发送信号
         	    self.parse_queue.task_done()
            except Exception:
                pass
              
         def parse(self,data):
            #解析页面
            #创建etree
            html = etree.HTML(data)
            # 获取页面列表
            div_list = html.xpath('//div[contains(@id, "qiushi_tag_")]')
            items = []
            for div in div_list:
                  head_shot = div.xpath('.//img/@src')[0]
            name = div.xpath('.//h2/text()')[0].strip('\n')
            content = div.xpath('.//span/text()')[0].strip('\n')
            item = {
                'head_shot': head_shot,
                'name': name,
                'content': content
            }
            items.append(item)
        with lock:
            self.fp.write(json.dumps(items, ensure_ascii=False) + '\n')

           
def mian():
    #1 基础url
    base_url ='https://www.qiushibaike.com/8hr/page/%d/'
    # spider_queue中存放要爬取的数据
    for i in range(1,11) # 从1-10页
    	url = base_url%i
        spider_queue.put(url)
        
    #2 创建spider线程
    for i in range(4)
    	SpiderThread(i,spider_queue,parse_queue).start()
    
    #3 创建解析线程，并保存数据到文件中
    fp = open('./data.txt','w',encoding='utf-8')
    for i in range(4)
    	ParseThread(i,parse_queue,fp).start()
    
    #4 设置解析线程退出标志
    global parse_exit_flag
    parse_exit_flag = True
    
    #5 关闭文件
    fp.close()
```



### Beautiful Soup:

获取查询标签字符串：  find_all .string / get.text() 

##### 注意：Beautiful Soup 的下标是从0开始，Xpath 是从1开始

```python


和lxml一样 Beautiful soup 也是和一个HTML/xml的解析器 主要功能是如何解析和提取HTML/XML的数据
4种对象属性：
BeautifulSoup：对象表示的是一个文档的内容
Tag:通俗的讲是HTML中的一个个标签
NavigableString：  获得标签后通过.string获取内容
Comment:对象是一个特殊类型的NavigableString对象 其输出内容不包括注释符号

 创建文档数 以lxml解析
 soup = BeautifulSoup(response.read().'lxml')
 
 soup.find(id='a') # 默认查找第一个 a 标签
 
 搜索文档树：
 soup.find_all(name,str,attr,recursive,text，**kwargs)  #recursive 递归循环的
 soup.find_all('a') 字符串   
 # 正则表达式用法 
 for tag in soup.find_all(re.comple('^\d$'))
 soup.soup.find_all(['a','b']) # 传列表
 #传keyword参数：
 soup.find_all(id='a')
 soup.find_all(class='sister')
 
 css选择器：
1通过标签查找：soup.select('title')
2通过类名查找：soup.select('.sister')
3通过id查找: soup.select('#link')
4组合查找：soup.select(p#link) # 查找p标签中id为link
  查找子标签：soup.select('head > title')
5加入属性元素的查找，属性需要使用中括号括起来，若属性和标签属于同一节点，中间不能加空格否则无法匹配到
soup.select('a[class="sister"]')
soup.select('a[href="http://example.com/elsie"]')
soup.select('pa[href="http://example.com/elsie"]')

#以上的select方法放回的结果都是列表形式，可以遍历形式输出  然后用get_text()的方法获取内容
soup.select('title')[0].get_text()
for title in soup.select('title'):
          title.get.text()
                  
                  
            
```

```python
import re

from bs4 import BeautifulSoup


soup = BeautifulSoup(open('./soup_test.html', 'r', encoding='utf-8'), 'lxml')

# print(type(soup), soup.name, soup.attrs)  #<class 'bs4.BeautifulSoup'> [document] {}
# # print(soup)
# # 通过.查询标签
print(type(soup.li)) #<class 'bs4.element.Tag'>
print(soup.li.string) #秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山
print(type(soup.li.string)) #<class 'bs4.element.Comment'>
print(soup.head.title) #<title>soup测试</title>
#
a = soup.find_all('a', {'name':'he'})
print(type(a[1].string))
print(a[0].string)  #人面不知何处去，桃花依旧笑春风#
#
# # 直接子节点
print(soup.head.children)
#
for item in soup.head.children:
    print(item.string)   # \n  None \n soup测试 \n

print(soup.head.contents)  #['\n', <meta charset="utf-8"/>, '\n', <title>soup测试</title>, '\n']
print(len(soup.head.contents))   # 5
# 、#
# 子孙
print(soup.body.descendants)
#
for item in soup.body.descendants:
    print(item)  # 所有的网页节点

# find  查找单个标签
print(soup.find('a'))
#<a href="http://www.baidu.com" title="出塞"><!--秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山--></a>

# print(soup.find('a', href="https://www.baidu.com"))

#find_all  查找所有的
print(soup.find_all('a', href="https://www.baidu.com"))  # 找到所有带 属性href的a标签 返回一个列表
#
print(soup.find_all(re.compile(r'li.*')))   #找到所有的匹配到的li标签
#
print(soup.find_all(['a', 'title'])) # 找到所有的a 标签和 title 标签
#
print(soup.find_all(class_='hello'))
print(soup.find_all(id='world'))
#
# # 使用css选择器
print(soup.select('a'))
#
# 通过class查找
print(soup.select('.hello'))
#
# 通过id查找
print(soup.select('#world'))

# 通过属性来查
print(soup.select('a[name="he"]'))


# 组合查找
print(soup.select('p.jiang')) #<span>三国猛将</span>
#
print(soup.select('li#world'))
#[<li class="hello" id="world"><a href="http://www.baidu.com"
# title="出塞"><!--秦时明月汉时关，万里长征人未还，但使龙城飞将在，不教胡马度阴山--></a></li>]

#
# # 通过标签来组合查找
print(soup.select('head > title'))  #[<title>soup测试</title>]
#
print(soup.select('body > div > ul > li > a'))



```

```python
# 面向对象爬取智联招聘

import requests
from bs4 import BeautifulSoup
import urllib.parse
import urllib.request


class ZhiLian(object):
    def __init__(self,url,start_page,end_page,area,gangwei):

        self.url = url
        self.start_page = start_page
        self.end_page = end_page
        self.area = area
        self.gangwei = gangwei
        self.headers = {
            'User-Agent':'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537'
        }

    def download(self,request):
        html = request.text.encode('utf-8')
        soup = BeautifulSoup(html,'lxml')
        # print(soup)
        table_list = soup.select('#newlist_list_content_table > table')[1:]
        for table in table_list:
            # 获取职位名称：
            zwmc = table.select('.zwmc')[0].select('a')[0].get_text()
            zwmc.strip() # 去除空格
            gsmc = table.select('.gsmc > a')[0].get_text().strip()
            salary = table.select('.zwyx')[0].get_text()
            print(gsmc)

    def handler_url(self,page):
        data = {
            'ji': self.area,
            'kw': self.gangwei,
            'p': page,
        }
        data = urllib.parse.urlencode(data)
        url = self.url + data
        request = requests.get(url = url, headers =self.headers)
        return request

    def start(self):
        for page in range(self.start_page,self.end_page+1):
            requests = self.handler_url(page)
            self.download(requests)

def main():
    url = 'http://sou.zhaopin.com/jobs/searchresult.ashx?'
    start_page = int(input('请输入:'))
    end_page = int(input('请输入:'))
    area = input('请输入地区:')
    gangwei = input('请输入岗位:')

    obj = ZhiLian(url, start_page, end_page, area, gangwei)
    obj.start()


if __name__ == '__main__':
    main()

```



### selenium：

```python
<input type='text' name='password' id='password-id' />


driver = webdriver.Chrome()

#无头模式
options = webdriver.ChromeOptions()
options.add_argument('headless')
driver=webdriver.Chrome(chrome_options=options)

#访问网页图片不加载
options = webdriver.ChromeOptions()
prefs={'profile.default_content_setting_values':{'images':2}}
options.add_experimental_option('perfs',prefs)
driver = webdriver.Chrome(chrome_options=options)

# 获取方法
element = driver.find_element_by_id('passwd-id')
element = driver.find_element_by_name('password')
	...= driver.find_element_by_tag_name('input')

##找到所有的div 返回一个可迭代的对象   
element= driver.find_elements_by_xpath('div[class@="index"]')  

# 获取div的字符串  #获取查找标签的字符串 (....).text
 ...= driver.find_element_by_xpath('//input[@id="password-id"]').text
而且你在用 xpath 的时候还需要注意的是，如果有多个元素匹配了 xpath，它只会返回第一个匹配的元素。如果没有找到，那么会抛出 NoSuchElementException 的异常。

driver.close() #关闭当前
driver.quit()  #退出全部

#增加cookies属性
driver.add.cookies({'xx':'xx'})

# 抓取cookies
driver.get_cookies()

# 判断页数
 if driver.page_source.find('shark-pager-disable-next') != -1:  
#  page.source:driver的一个判断“页数”类方法 find如果没有找到就返回-1

#滚动条下拉：
driver.execute_script('document.documentElement.scrollTop=10000')
# 记得让程序睡几秒等待加载完成

#写入文档：
fp = open('./douyu.txt','w',encoding='utf-8') 

fp.write(json.dumps(items,ensure_ascii=False))

# 点击进入一个窗口
driver.find_element_by_xpath('//a[@class="lnk-movie"]').click()
#  获取窗口列表
handles = driver.window_handles    
time.sleep(2)
# 进入第一个窗口
driver.switch_to.window[handles[0]]

#获取属性值  get_attribute('src')
src = driver.find_element_by_xpath('//div[@class="video"]').get_attribute('src')
# 循环访问详情页地址
for href, i in zip(href_list, range(len(href_list))):
    driver.get(href)
    sleep(2)
    # 找到页面中要下载的视频地址。
    src = driver.find_element_by_xpath('//div[@class="player-wrap"]//video').get_attribute('src')
    # print(src)
    # 下载视频
    urlretrieve(src, './xigua/' + random_str() + '.mp4')
    print('第%d个视频下载完成' % (i+1,))

#下载视频
from urllib.request import urlretrieve
urlretrieve(video_href, filename='xxx') # 参数1:下载地址 参数2：下载文件的自定义名称
#保存图片：
urlretrieve(img_url, 'captcha.jpg')

#截图：
driver.save_screenshot('douban.png')

#向文本输入内容：
element.send_keys('text')
# 利用keys 模拟点击摸个按键
element.send_keys('text',keys.ARROW_DOWN)
element.clear() # 若输入文本不自动清除 可用这个清除输入文本内容


```



### 设置日志：

```python
级别：
	CRITICAL：严重错误
	ERROR：一般错误
	WARNING：警告
	INFO: 一般信息
	DEBUG：调试信息

	默认的级别为DEBUG，会显示上面所有的信息
	在配置文件中  settings.py
	LOG_FILE  : 将屏幕显示的信息全部记录到文件中，屏幕不再显示
	LOG_LEVEL : 设置日志显示的等级，就是显示哪些，不显示哪些    

```



### Scrapy-Post请求：

#### 百度翻译：

```py
scrapy startproject baidu
scrapy genspider baidufangyi "www.baidu.com"

# 启动 scrapy crawl xx

def start_request(self):
	post_url = ''
	formdata = (
		'kw':'hello'
	)
	yield scrapy.FormRequest(url = url,formdata = formdata,headers = headers)

#解析
def parse(self,response):
	with open('./baidu.txt','w' encoding='utf-8') as fp:
		fp.write(josn.dumps(josn.loads(response.text),ensure_ascii=False))
# josn.loads 转化成python对象  在 dumps 写入文档
```

### Scrapy 代理：

在setting中 打开 downloader_middlewares

名字改为类名 “ ProxyMiddlewares ”

```python
#重写代理
# 在middlewares.py 
class ProxyMiddlewares(object):
    request.meta['proxy']='https://101.101.10.1:9000'
	return None
<------------------------------------------------->
import scrapy
import json

class BaiduSpider(scrapy.Spider):
    name = 'baidu'
    allowed_domains = ['www.baidu.com']

    # http://fanyi.baidu.com/sug
    # start_urls = ['http://www.baidu.com/']

    # def parse(self, response):
    #     pass

    # 如果想要自己直接发送post请求，则需要重写这个方法。
    # 这个方法以前就是遍历start_urls列表，生成请求对象的
    def start_requests(self):
    	post_url = 'http://fanyi.baidu.com/sug'
    	word = 'world'
    	data = {
    		'kw': word
    	}
    	yield scrapy.FormRequest(url=post_url, formdata=data, callback=self.parse_post)

    def parse_post(self, response):
    	print('---------------------',json.loads(response.text))

```

#### 模拟豆瓣登录：

```python
# -*- coding: utf-8 -*-
import scrapy
import urllib.request
from PIL import Image

class DoubanSpider(scrapy.Spider):
    name = 'douban'
    allowed_domains = ['douban.com']
    start_urls = ['https://accounts.douban.com/login']

    def parse(self, response):
        # 去查找有没有验证码
        # 【注】如果找不到，那么返回None
        image_url = response.xpath('//img[@id="captcha_image"]/@src').extract_first()
        # 如果没有验证码图片，image_url就是None
        if not image_url:
            data = {
                'source': '',
                'redir': 'https://www.douban.com',
                'form_email': '18513106743',
                'form_password': '31415926abc',
                'login': '登录',
            }
        else:
            # 获取验证码id
            captcha_id = response.xpath('//input[@name="captcha-id"]/@value').extract_first()
            # 保存验证码图片，并且提示用户输入验证码
            urllib.request.urlretrieve(image_url, './douban.png')
            Image.open('./douban.png').show()
            yanzhengma = input('请输入验证码:')
            data = {
                'source': '',
                'redir': 'https://www.douban.com',
                'form_email': '18513106743',
                'form_password': '31415926abc',
                'captcha-solution': yanzhengma,
                'captcha-id': captcha_id,
                'login': '登录',
            }
        post_url = 'https://accounts.douban.com/login'
        print('-------------------------------------------------------')
        return scrapy.FormRequest(url=post_url, formdata=data, callback=self.accounts)

    def accounts(self, response):
        with open('douban1.html', 'wb') as fp:
          fp.write(response.body)
        url = 'https://www.douban.com/accounts/'
        print('++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++')
        return scrapy.Request(url=url, callback=self.parse_accounts)

    def parse_accounts(sef, response):
        print('===================================================================')
        with open('豆瓣.html', 'wb') as fp:
            fp.write(response.body)

```

#### 模拟微博登录：

```python
# -*- coding: utf-8 -*-
import scrapy


class WeiboSpider(scrapy.Spider):
    name = 'weibo'
    allowed_domains = []
    # start_urls = ['http://www.weibo.cn/']

    # def parse(self, response):
    #     pass


    def start_requests(self):
        post_url = 'https://passport.weibo.cn/sso/login'
        data = {
            'username': '18515178949',
            'password': '31415926abc',
            'savestate': '1',
            'r': '',
            'ec': '0',
            'pagerefer': 'http://weibo.cn/',
            'entry': 'mweibo',
            'wentry': '',
            'loginfrom': '',
            'client_id': '',
            'code': '',
            'qq': '',
            'mainpageflag': '1',
            'hff': '',
            'hfp': '',}
        headers = {
            'Referer': 'https://passport.weibo.cn/signin/login?entry=mweibo&r=http%3A%2F%2Fweibo.cn%2F&backTitle=%CE%A2%B2%A9&vt=',
        }
        yield scrapy.FormRequest(url=post_url, formdata=data, callback=self.lala, headers=headers)

    def lala(self, response):
        # print(response.text)
        url = 'https://weibo.cn/6433251843/info/'
        # 再次发送request请求即可
        yield scrapy.Request(url=url, callback=self.haha)

    def haha(self, response):
        with open('weibopo.html', 'w', encoding='utf-8') as fp:
            fp.write(response.text)

```



### Scrapy Cookie:

```py
在setting 中 打开 cookie 
```



### Scrapy:spider

##### 1，通过指令创造文件：scrapy startproject 项目名

##### 2，cd 到目标文件 -- scrapy genspider  name  'www.baidu.com' (name : 爬虫的名字 后面是url)

##### 3,  运行 scrapy crawl xxx -o data.json    编码问题：E

​	scrapy crawl xxx -o data.xml    scrapy crawl xxx -o data.csv

##### 4，调试 ：scrapy shell "网址" 

如果报错：error .ConnectionDore:Connection was closed......

在python<3.6>中找到site-packages -- scrapy包 ----setting --default_setting.py  加入：

```
USER_AGENT ='Mozilla/5.0(Macintosh;IntelMacOSX10.6;rv:2.0.1)Gecko/20100101Firefox/4.0.1'
将原来的代码注释
```

```python
# -*- coding: utf-8 -*-
import scrapy
from movieproject.items import MovieprojectItem

class MovieSpider(scrapy.Spider):
    name = 'movie'
    allowed_domains = ['www.dytt8.net']  # 允许的域名
    start_urls = ['http://www.dytt8.net/html/gndy/dyzz/index.html']  #爬虫起始的url 
    
    # parse 函数必须返回的是一个可以迭代的对象
    def parse(self, response):
        # 找到所有的电影
        table_list = response.xpath('//div[@class="co_content8"]/ul//table')
        # 遍历所有的电影列表，得到电影的详细信息
        for table in table_list:
            # 在当前的页面的只能提取到两个信息，一个是name，一个是movie_info
            # 创建一个对象
            item = MovieprojectItem()
            # 提取对应的信息  
            # 【注】在点的后面要加两个杠
            item['name'] = table.xpath('.//a[@class="ulink"]/text()').extract_first()
            item['movie_info'] = table.xpath('.//tr[last()]/td/text()').extract_first()
            # 获取电影的链接
            movie_url = 'http://www.dytt8.net' + table.xpath('.//a[@class="ulink"]/@href').extract_first()
            # yield item

            # 这里面涉及到一个传递item的问题，我们要学习如何传参,加上一个meta参数，meta参数是一个字典，过去之后，通过字典的键获取其值
            yield scrapy.Request(url=movie_url, callback=self.parse_info, meta={'item': item})

    # 获取item的其它信息
    def parse_info(self, response):
        # 获取到传递过来的参数
        item = response.meta['item']
        # 接着解析网页，获取item的其它信息
        item['image_url'] = response.xpath('//div[@id="Zoom"]//img[1]/@src').extract_first()
        # item['story_info'] = response.xpath('')
        item['download_url'] = response.xpath('//td[@bgcolor="#fdfddf"]/a/text()').extract_first()

        yield item
```

### Spider:spider 爬取多页

```python
# -*- coding: utf-8 -*-
import re

import scrapy
from movie.items import MovieItem
import os

class DyttSpider(scrapy.Spider):
    name = 'dytt'
    allowed_domains = ['www.ygdy8.net']  # 允许的域名
    # 爬虫起始的url 是一个列表 里面可以写多个
    start_urls = ['http://www.ygdy8.net/html/gndy/dyzz/index.html']

    # parse 函数必须返回一个可迭代的对象
    def parse(self, response): # response 是一个解析对象 从对象中获取html字符串进行解析
        table_list = response.xpath('//table[contains(@class, "tbspan")]')
        for table in table_list:
            title = table.xpath('.//a/text()').extract_first()
            brief = table.xpath('.//tr[last()]/td/text()').extract_first()
            link = "http://www.ygdy8.net" + table.xpath('.//a/@href').extract_first()
            item = MovieItem()
            item['title'] = title
            item['brief'] = brief
            item['link'] = link
            # 最大页码
            pattern = re.compile(r'共(\d+)页')
            max_page = int(pattern.findall(response.text)[0])
            yield item
        for page in range(2, max_page+1):
            url = 'http://www.ygdy8.net/html/gndy/dyzz/list_23_%d.html' % page
            yield scrapy.Request(url, callback=self.parse)



```

### CrawlSpider 爬虫案例

CrawlSpider可以定义规则，再解析html内容的时候，可以根据链接规则提取出指定的链接，然后再向这些链接发送请求

所以，如果有需要跟进链接的需求，意思就是爬取了网页之后，需要提取链接再次爬取，使用CrawlSpider是非常合适的

#### CrawlSpider: cmoive 

创建项目: scrapy startproject dushuproject

创建爬虫类：scrapy genspider -t crawl read www.dushu.com

```python
# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from movieproject.items import MovieprojectItem


class MmSpider(CrawlSpider):
    name = 'mm'
    allowed_domains = ['www.dytt8.net']
    start_urls = ['http://www.dytt8.net/html/gndy/dyzz/index.html']

    rules = (
        Rule(LinkExtractor(allow=r'list_23_\d+\.html'), callback='parse_table', follow=False),
    )
 # 注意：callback只能写函数名字符串, callback='parse_item'
 # 在基本的spider中，如果重新发送请求，那里的callback写的是   callback=self.parse_item

    def parse_table(self, response):
        # 找到所有的电影
        table_list = response.xpath('//div[@class="co_content8"]/ul//table')
        # 遍历所有的电影列表，得到电影的详细信息
        for table in table_list:
            # 在当前的页面的只能提取到两个信息，一个是name，一个是movie_info
            # 创建一个对象
            item = MovieprojectItem()
            # 提取对应的信息  
            # 【注】在点的后面要加两个杠
            item['name'] = table.xpath('.//a[@class="ulink"]/text()').extract_first()
            item['movie_info'] = table.xpath('.//tr[last()]/td/text()').extract_first()
            # 获取电影的链接
            movie_url = 'http://www.dytt8.net' + table.xpath('.//a[@class="ulink"]/@href').extract_first()
            # yield item

            # 这里面涉及到一个传递item的问题，我们要学习如何传参,加上一个meta参数，meta参数是一个字典，过去之后，通过字典的键获取其值
            yield scrapy.Request(url=movie_url, callback=self.parse_info, meta={'item': item})

    def parse_info(self, response):
        # 获取到传递过来的参数
        item = response.meta['item']
        # 接着解析网页，获取item的其它信息
        item['image_url'] = response.xpath('//div[@id="Zoom"]//img[1]/@src').extract_first()
        # item['story_info'] = response.xpath('')
        item['download_url'] = response.xpath('//td[@bgcolor="#fdfddf"]/a/text()').extract_first()
        yield item

```

##### CrawlSpider：读书网

##### spiders:

```python
class ReadSpider(CrawlSpider):
    name = 'read'
    allowed_domains = ['www.dushu.com']
    start_urls = ['https://www.dushu.com/book/1163.html']
    # 假设我把follow修改为True，那么爬虫会从start_urls爬取的页面中在寻找符合规则的url，如此循环，直到把全站爬取完毕
    rules = (
        Rule(LinkExtractor(allow=r'/book/1163_\d+\.html'), callback='parse_item',follow=True),
    )

    def parse_item(self, response):
        # 首先查找到所有的书籍
        book_list = response.xpath('//div[@class="bookslist"]/ul/li')
        # 遍历所有书籍，获取每本书详细的内容
        for book in book_list:
            item = DushuprojectItem()
            item['image_url'] = book.xpath('./div[@class="book-info"]/div/a/img/@data-original').extract_first()
            item['book_name'] = book.xpath('./div[@class="book-info"]/h3/a/text()').extract_first()
            item['author'] = book.xpath('./div[@class="book-info"]/p/a/text()').extract_first()
            item['info'] = book.xpath('./div[@class="book-info"]/p[@class="disc eps"]/text()').extract_first()
            yield item

        yield item

```

##### items:

```python
import scrapy


class DushuprojectItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    image_url = scrapy.Field()
    book_name = scrapy.Field()
    author = scrapy.Field()
    info = scrapy.Field()
```

##### setting:

```py
ITEM_PIPELINES = {
   'dushuproject.pipelines.DushuprojectPipeline': 300,
   'dushuproject.pipelines.MysqlPipeline': 299,
}

DB_HOST = '127.0.0.1'
DB_PORT = 3306
DB_USER = 'softpo'
DB_PWD = 'root'
DB_NAME = 'test'
DB_CHARSET = 'utf8'
```

##### pipelines:保存到本地：

```python
class DushuprojectPipeline(object):
    def open_spider(self, spider):
        self.fp = open('book.json', 'w', encoding='utf-8')

    def process_item(self, item, spider):
        obj = dict(item)
        string = json.dumps(obj, ensure_ascii=False)
        self.fp.write(string + '\n')
        return item

    def close_spider(self, spider):
        self.fp.close()
```

##### pipelines:保存到mysql数据库：

```python
class MysqlPipeline(object):
    """docstring for MysqlPipeline"""
    def __init__(self):
        settings = get_project_settings()
        self.host = settings['DB_HOST']
        self.port = settings['DB_PORT']
        self.user = settings['DB_USER']
        self.pwd = settings['DB_PWD']
        self.name = settings['DB_NAME']
        self.charset = settings['DB_CHARSET']
        self.connect()

    def connect(self):
        self.conn = pymysql.connect(host=self.host,
                             port=self.port,
                             user=self.user,
                             passwd=self.pwd,
                             db=self.name,
                             charset=self.charset)
        self.cursor = self.conn.cursor()

    def close_spider(self, spider):
        self.conn.close()
        self.cursor.close()

    def process_item(self, item, spider):
        sql = 'insert into book(image_url, book_name, author, info) values("%s", "%s", "%s", "%s")' % (item['image_url'], item['book_name'], item['author'], item['info'])
        # 执行sql语句
        self.cursor.execute(sql)
        self.conn.commit()
        return item
```





### Redis-Scrapy 分布式策略：

master （核心的服务器）端：使用Centos \ubuntu\mac\window 都可，搭建一个rides

数据库，不负责爬，只负责url指纹判重,Request的分配，以及数据的存储

Slaver端 ：负责执行爬虫程序，运行过程中提交新的Request 给Master 

##### 下载redis：

```python
网址：https://redis.io/download
$ wget http://download.redis.io/releases/redis-3.2.7.tar.gz
$ tar xzf redis-3.2.7.tar.gz
$ cd redis-3.2.7
$ make
启动服务： $ src/redis-server
1，拷贝一份 redis.conf 到任意目录  建议目录 /etc/redis/redis.conf 
   将本地ip给注释掉  #band 127.0.0.1  然后其他ip（远程爬虫端）就可以访问
2，sodu redis-server /etc/redis/redis.conf #开启服务 
  daemonize yes/no # 是否作为守护进程
  设为no：运行redis-server /etc/redis/redis.conf 将显示redis启动提示画面 
  设为yes：默认在后台运行
3，
```



### RedisCrawlSpider: movie分布式爬虫

#### 1,pycharm ：cd到spiders文件夹下

#### 2, scrapy runspider xxx.py  启动文件

#### setting:文件配置：

##### 1，使用的是scrapy_redis的去重类……

DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"

##### 2，调度器使用是scrapy_redis的调度器 不使用默认调度器
SCHEDULER = "scrapy_redis.scheduler.Scheduler"

默认使用scrapy-redis(按优先顺序)队列形式

SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.SpiderPriorityQueue'

队列形式先进先出：scrapy_redis.queue.SpiderQueue

栈形式，先进后出：scrapy_redis.queue.SpiderStack

##### 3爬取的过程中是否允许暂停 "重要"

SCHEDULER_PERSIST = True

##### 4,scrapy_radis.pipelines.RedisPipelines  支持将数据存储到数据库中

注意优先级问题：先放在本地，在放到数据库中

ITEM_PIPELINES={

  'movieproject.pipelines.MovieprojectPipeline': 300,
   'scrapy_redis.pipelines.RedisPipeline': 400,

}

##### 5,配置存储redis服务器  

指定的数据库ip：

REDIS_HOST = '10.0.112.191'

指定的数据库端口：

REDIS_PORT = 6379

##### 6，关闭redis保护模式，是远端ip可以访问（linux命令）

redis-server --protected-mode no 

连接redis 检测服务器是否开启 systemctl status redis

，sodu redis-server /etc/redis/redis.conf #开启服务 

##### 7，访问延时：

减少web服务器压力 ，尽量减低访问频率

DOWNLOAD_DELAY = 1

```python
#master端向队列中添加起始url
linux :redis-cli / redis-server /etc/redis/redis.conf
window: cd 到 redis文件目录 redis-cli 
然后 启动 redis desk manager 连接数据库--
redis-cli
127.0.0.1:6379> lpush 'fen:start_urls' 'http://www.dytt8.net/html/gndy/dyzz/index.html'
(lpush redis_key + start_urls) 创建爬虫起始页面
lpush ：从左往右进
```



```python
# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from movieproject.items import MovieprojectItem
from scrapy_redis.spiders import RedisCrawlSpider


class MmSpider(RedisCrawlSpider):
    name = 'fen'
    allowed_domains = ['www.dytt8.net']
    # start_urls = ['http://www.dytt8.net/html/gndy/dyzz/index.html']
    # 启动所有slaver端爬虫命令
    redis_key = 'fen:start_urls'
    redis_encoding = 'utf-8'

    rules = (
        Rule(LinkExtractor(allow=r'list_23_\d+\.html'), callback='parse_table', follow=True),    
    )

    def parse_table(self, response):
        # 找到所有的电影
        table_list = response.xpath('//div[@class="co_content8"]/ul//table')
        # 遍历所有的电影列表，得到电影的详细信息
        for table in table_list:
            # 在当前的页面的只能提取到两个信息，一个是name，一个是movie_info
            # 创建一个对象
            item = MovieprojectItem()
            # 提取对应的信息  
            # 【注】在点的后面要加两个杠
            item['name'] = table.xpath('.//a[@class="ulink"]/text()').extract_first()
            item['movie_info'] = table.xpath('.//tr[last()]/td/text()').extract_first()
            # 获取电影的链接
            movie_url = 'http://www.dytt8.net' + table.xpath('.//a[@class="ulink"]/@href').extract_first()
            # yield item

            # 这里面涉及到一个传递item的问题，我们要学习如何传参,加上一个meta参数，meta参数是一个字典，过去之后，通过字典的键获取其值
            yield scrapy.Request(url=movie_url, callback=self.parse_info, meta={'item': item})

    def parse_info(self, response):
        # 获取到传递过来的参数
        item = response.meta['item']
        # 接着解析网页，获取item的其它信息
        item['image_url'] = response.xpath('//div[@id="Zoom"]//img[1]/@src').extract_first()
        # item['story_info'] = response.xpath('')
        item['download_url'] = response.xpath('//td[@bgcolor="#fdfddf"]/a/text()').extract_first()

        yield item

```



















