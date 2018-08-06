# day07

###线程锁和队列锁

```python
# 线程锁和队列锁。
# 要一步步写出来。
# 基本思路就是创建一个队列，往里放10个数据，创建一个线程不停的从队列中取数据。
# coding=utf-8
import threading

import queue

# 创建queue
q = queue.Queue(10)

# 加一个全局标志，让线程能判断何时退出。
exit_flag = False

class MyThread(threading.Thread):
    def __init__(self, q, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.q = q

    def run(self):
        # 不停的从队列中取数据。
        # 死循环
        while True:
            # 增加退出判断
            global  exit_flag
            if exit_flag:
                break
            # 取数据，并设置不等待
            try:
                value = self.q.get(block=False)
                # 取任务完成, 取出后向队列发送一个信号。队列的join方法以此来判断队列是否为空。如果取之后不调用task_done，则join将挂死。
                q.task_done()
            except:
                pass
            print('get data %d' % value)




def main():
    # 往队列中放10个数据
    print('主线程开始')
    for i in range(10):
        q.put(i)

    # 启动多线程
    mythread = MyThread(q)
    mythread.start()
    # 等待线程执行完成后设置退出标志， 这就是所谓的线程锁。
    # mythread.join()
    # 设置退出标志为True，上面的线程没跑完，下面的设置代码永远不会做。而线程中线程要退出又依赖于退出标志。

    # 设置队列锁，队列为空才会继续做。这样当线程把队列清空之后，队列锁就释放了。
    q.join()
    global  exit_flag
    exit_flag = True
    print('主线程结束')


if __name__ == '__main__':
    main()
```

### 多线程爬虫

实现原理图：

![](F:\新建文件夹\爬虫备课\day07\多线程爬虫实现原理.png)

基本思路：

```
需要设置两个队列，和解析线程的退出标志
1. 创建爬虫线程，并且启动。
2. 创建解析线程，启动，并保存数据到本地文件中。
3. 队列锁，保证任务执行结束。
4. 设置退出标志为True，退出线程，工作结束。
5. 关闭文件
```

请大家仔细研究代码：

```python
import json
from urllib.error import URLError

import requests
import queue
import threading
import random

import time
from lxml import etree

# 创建2个队列
# 创建爬虫队列
spider_queue = queue.Queue()

# 创建解析队列
parse_queue = queue.Queue()

# 设置解析线程退出标志
parse_exit_flag = False


# 基础url
base_url = 'https://www.qiushibaike.com/8hr/page/%d/'

# 文件操作锁
lock = threading.Lock()

# headers 列表
UserAgent_List = [
    "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.1 Safari/537.36",
    "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2226.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.4; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2225.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2224.3 Safari/537.36",
    "Mozilla/5.0 (Windows NT 10.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/40.0.2214.93 Safari/537.36",
    "Mozilla/5.0 (Windows NT 10.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/40.0.2214.93 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2049.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 4.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2049.0 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.67 Safari/537.36",
    "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.67 Safari/537.36",
    "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.3319.102 Safari/537.36",
    "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.2309.372 Safari/537.36",
    "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.2117.157 Safari/537.36",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/35.0.1916.47 Safari/537.36",
    "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/34.0.1866.237 Safari/537.36",
]


class SpiderThread(threading.Thread):
    def __init__(self, id, s_queue, p_queue, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.id = id
        self.spider_queue = s_queue
        self.parse_queue = p_queue

    def run(self):

        print('%d线程开始执行' % self.id)
        # 循环从爬虫队列中取数据。
        while True:
            # 当队列为空时，终止循环
            if self.spider_queue.empty():
                break
            # 取数据
            page = self.spider_queue.get()

            # 拼接url
            url = base_url % page
            # 避免因为网络不好数据无法取出，尝试四次。
            times = 4
            while times > 0:
                try:
                    response = requests.get(url=url, headers={'User-Agent': random.choice(UserAgent_List)})
                    # 把数据放入解析队列
                    self.parse_queue.put(response.text)
                    # 爬的时候不要太快，睡1秒
                    time.sleep(1)
                    print('%d线程成功抓取第%d页的数据' % (self.id, page))
                    # 事情做完才 通知队列数据取出
                    self.spider_queue.task_done()
                    # 成功一次就break
                    break
                except URLError as e:
                    print('网络错误')
                finally:
                    times -= 1


class ParseThread(threading.Thread):
    def __init__(self, id, p_queue, fp, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.id = id
        self.parse_queue = p_queue
        self.fp = fp

    def run(self):
        global parse_exit_flag
        # 循环解析队列中的数据
        while True:
            # 当解析退出标志为True时，退出循环
            if parse_exit_flag:
                break
            # 解析有可能出错。
            try:
                # 从解析队列中取数据, 注意设置成False
                data = self.parse_queue.get(block=False)
                # 解析数据，封装成一个函数
                self.parse(data)
                print('%d线程解析数据成功' % (self.id))
                # 告诉队列取完了数据
                self.parse_queue.task_done()


            except Exception as e:
                pass
                # print(e, '解析错误')

    def parse(self, data):
        global parse_num
        # 数据保存到一个文件中。
        # 先解析数据
        # 创建etree对象
        html = etree.HTML(data)
        # 找出页面上所有的段子div
        div_list = html.xpath('//div[contains(@id,"qiushi_tag_")]')
        # 对div_list中的每一个div进行数据解析
        results = []
        for div in div_list:
            # 头像的url
            head_shot = div.xpath('.//img/@src')[0]
            # 作者名字
            name = div.xpath('.//h2')[0].text
            # 内容
            content = div.xpath('.//span')[0].text.strip('\n')
            # 保存到一个字典中
            item = {
                'head_shot': head_shot,
                'name': name,
                'content': content
            }
            results.append(item)
            # 保存到文件中，并且加锁
        json_result = {
            'msg': 'ok',
            'status': 200,
            'data': results
        }
        with lock:
            self.fp.write(json.dumps(results, ensure_ascii=False) + '\n')


def main():
    print('--------------任务开始')
    # 假设爬10页数据。往爬虫队列中写入10个数
    for page in range(1,11):
        spider_queue.put(page)

    # 1. 创建爬虫进程，并启动
    # 创建3个爬虫线程
    for i in range(3):
        SpiderThread(i, spider_queue, parse_queue).start()

    # 2. 创建解析进程并启动。
    # 创建3个解析线程，并保存解析的内容
    # 数据保存到一个文件中
    fp = open('./qiubai.txt', 'w', encoding='utf-8')
    for i in range(3):
        ParseThread(i, parse_queue, fp).start()

    # 3. 队列锁，保证任务执行结束。
    spider_queue.join()
    parse_queue.join()

    # 4. 设置关闭退出标志。
    global parse_exit_flag
    parse_exit_flag = True

    # 关闭文件
    fp.close()

    print('--------------任务完成')
if __name__ == '__main__':
    main()
```



#### selenium 

```python
#更改路径
#phantomjs.exe 文件存放的路径
#driver = webdriver.PhantomJS() 点击PhantomJs
# 改为：executable_path="D:/phantomjs-2.1.1-windows/bin/phantomjs.exe" 
#driver = webdriver.Chrome() 把chromedriver.exe放在python36包中


from selenium import webdirver
from time import sleep

# 创建对象
driver = webdriver.Chrome()

driver.get('https://v.taobao.com/v/content/live?catetype=704')

sleep(3)

#保存图片 
driver.save_screenshot('baidu.png')
# 退出
driver.quit()

```

##### 点击百度

```pyth
from webdriver import webdriver
import time 

driver = webdriver.Chrome()
driver.get('http://www.baidu.com/')

time.sleep(2)
# 获取百度输入框
driver.find_element_by_id('kw').send_key('美女')

#点击百度一下
driver.find_element_by_id('su').click()
#退出
driver.back()
#前进
driver.forward()
#退出
driver.quit()








```

