+++
date = '2025-06-18T13:31:44+08:00'
draft = false
title = '爬虫笔记1'
categories = ["逆向"]
tags = ["js", "逆向", "python"]
+++

# 杂项

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



