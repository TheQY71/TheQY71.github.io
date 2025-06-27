+++
date = '2025-06-18T13:31:44+08:00'
draft = false
title = '爬虫笔记1'
categories = ["逆向"]
tags = ["js", "逆向", "python"]
+++

# 基础

## 1.概念

**同步**：任务按顺序执行，一个任务完成后才能开始下一个。

**异步**：任务不按顺序执行，可以同时进行多个任务，无需等待前一个任务完成。

**协程 (Coroutine)**：一种用户态的轻量级线程，由程序自身调度，而非操作系统。
    1.  **非抢占式调度**：协程主动让出CPU。
    2.  **上下文切换开销小**：只保存和恢复少量寄存器。
    3.  **高并发**：可在单线程内实现大量并发任务。
    4.  **适用于I/O密集型任务**。


## 2.图片懒加载

没有滑动到这部分，图像的url就不加载
```html
<img src="../static/common/com_images/img-loding.png"
            style="height: 124px"
            data-original="//scpic.chinaz.net/files/default/imgs/2024-12-20/2e5af06b2ff16788_s.jpg"
            class="lazy"
            alt="金光闪闪红色幕布背景图片"
/>
```
图像src中存储的是一个假地址，真实的图像地址是存在data-originial(别的地方可能不叫这个属性名)中的。


## 3.视频下载

```python
import requests
from lxml import etree
import os

url ="https://www.51miz.com/shipin/" 

headers={
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36",
    "Referer": "https://www.51miz.com/"
}

response = requests.get(url,headers=headers)

html = response.text

tree = etree.HTML(html)
mp4_urls = tree.xpath('//video[contains(@src, ".mp4")]/@src | //video/source[contains(@src, ".mp4")]/@src | //source[contains(@src, ".mp4")]/@src | //a[contains(@href, ".mp4")]/@href')

print(f"共找到 {len(mp4_urls)} 个视频:", mp4_urls)

# 下载所有视频
for idx, mp4_url in enumerate(mp4_urls, 1):
    # 处理相对路径
    if not mp4_url.startswith('http'):
        mp4_url = requests.compat.urljoin(url, mp4_url)
    print(f"正在下载第{idx}个视频: {mp4_url}")
    try:
        r = requests.get(mp4_url, headers=headers, stream=True, timeout=30)
        r.raise_for_status()
        filename = f"video_{idx}.mp4"
        with open(filename, 'wb') as f:
            for chunk in r.iter_content(chunk_size=8192):
                if chunk:
                    f.write(chunk)
        print(f"已保存为 {filename}")
    except Exception as e:
        print(f"下载失败: {mp4_url}, 错误: {e}")

```

## 4.模拟登陆

```python
import requests
from lxml import etree

headers = {
    'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36',
}

login_url = 'https://passport.17k.com/ck/user/login'
data = {
    "loginName": "15027900535",
    "password": "xxxxxxxx"
}

session = requests.Session()
#请求的目的是为了获取cookie将其保存到session对象中
session.post(url=login_url,headers=headers,data=data)

#携带cookie向书架的页面进行请求发送，获取书架信息
#书架中的数据是动态加载的数据
book_url = 'https://user.17k.com/ck/author2/shelf?page=1&appKey=2406394919'
page_text = session.get(url=book_url,headers=headers).json()

#解析书架信息
print(page_text)
```



