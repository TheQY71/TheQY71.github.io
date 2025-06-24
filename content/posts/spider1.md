+++
date = '2025-06-18T13:31:44+08:00'
draft = false
title = '爬虫笔记1'
categories = ["逆向"]
tags = ["js", "逆向", "python"]
+++

# 基础

## 图片懒加载

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


## 视频下载

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


