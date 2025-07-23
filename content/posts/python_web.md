+++
date = '2025-07-15T15:31:44+08:00'
draft = false
title = 'python web'
categories = ["web"]
tags = ["js", "jquery","fastAPI"]
+++

# 样例

## 1. 模拟加密ajax网站
点击不同的page，会返回不同的内容，传输的数据都使用aes加密

```
.
├── server.py
├── static
├── templates
   └── index.html
```

index.html
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>

<body>

  <h3>Hello Alex!</h3>
  <p>当前时间: {{now}}</p>
  <p>古典小说: </p>
  <p>
    <span onclick="get_page_books(0)">page1</span>
    <span onclick="get_page_books(1)">page2</span>
    <span onclick="get_page_books(2)">page3</span>
    <span onclick="get_page_books(3)">page4</span>
  </p>
  <p id="bookResult"></p>

  <!--js文件导入-->
  <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.7.1/jquery.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.2.0/crypto-js.min.js"></script>
  <script>
    // 获取四大名著
    var dom = document.getElementsByTagName("p")[1];
    // 加密函数
    function aes_encrypt(data) {
      var key = CryptoJS.enc.Utf8.parse('1234567890123456'); // 16字节密钥
      var iv = CryptoJS.enc.Utf8.parse('1234567890123456'); // 16字节IV
      var ciphertext = CryptoJS.AES.encrypt(data, key, {
        iv: iv,
        mode: CryptoJS.mode.CBC,
        padding: CryptoJS.pad.Pkcs7
      }).toString();
      return ciphertext;
    }
    // 解密函数
    function aes_decrypt(ciphertext) {
      var key = CryptoJS.enc.Utf8.parse('1234567890123456'); // 16字节密钥
      var iv = CryptoJS.enc.Utf8.parse('1234567890123456'); // 16字节IV
      var decrypted = CryptoJS.AES.decrypt(ciphertext, key, {
        iv: iv,
        mode: CryptoJS.mode.CBC,
        padding: CryptoJS.pad.Pkcs7
      });
      return decrypted.toString(CryptoJS.enc.Utf8);
    }

    function get_page_books(page) {
      params = {
        "page": aes_encrypt(page + "")
      }
      $.ajax({
        url: "/books", // 对应FastAPI的/books接口
        method: "GET",
        data: params,
        success: function (data) {
          console.log("data: " + data.toString());
          let resultDom = document.getElementById("bookResult");
          resultDom.innerHTML = data.map(item => "《" + aes_decrypt(item) + "》"); // 将结果展示在第三个p标签
        },
        error: function (xhr, status, error) {
          resultDom.innerText = "请求失败! " + error;
        }
      });
    };
  </script>
</body>
```

server.py
```python
from fastapi import FastAPI, Request, Form, HTTPException, Request,Query
from fastapi.responses import HTMLResponse, RedirectResponse
from fastapi.staticfiles import StaticFiles
from fastapi.templating import Jinja2Templates
from pydantic import BaseModel
from typing import Optional
import datetime  # 添加缺少的导入
import random    # 添加缺少的导入
import uvicorn   # 添加uvicorn导入
import os
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from Crypto.Random import get_random_bytes
import base64

# 创建FastAPI应用实例
app = FastAPI(title="测试站", description="使用FastAPI构建的示例网站")

# 配置静态文件目录
app.mount("/static", StaticFiles(directory="static"), name="static")

# 配置模板目录
templates = Jinja2Templates(directory="templates")

# 密钥和IV必须是16字节
key = b'1234567890123456'
iv = b'1234567890123456'

def aes_encrypt(data: str) -> str:
    """
    AES CBC模式加密
    :param data: 待加密的字符串
    :return: base64编码的密文
    """
    cipher = AES.new(key, AES.MODE_CBC, iv)
    # 对数据进行PKCS7填充
    padded_data = pad(data.encode('utf-8'), AES.block_size)
    ciphertext = cipher.encrypt(padded_data)
    return base64.b64encode(ciphertext).decode('utf-8')

def aes_decrypt(ciphertext_b64: str) -> str:
    """
    AES CBC模式解密
    :param ciphertext_b64: base64编码的密文
    :return: 解密后的字符串
    """
    cipher = AES.new(key, AES.MODE_CBC, iv)
    ciphertext = base64.b64decode(ciphertext_b64)
    decrypted_padded_data = cipher.decrypt(ciphertext)
    # 移除PKCS7填充
    original_data = unpad(decrypted_padded_data, AES.block_size)
    return original_data.decode('utf-8')

@app.get("/", response_class=HTMLResponse)
async def index(request: Request):
    now = datetime.datetime.now().strftime("%y/%m/%d %X")
    return templates.TemplateResponse("index.html", {"request": request, "now": now})

@app.get("/books")
async def get_books(page:str=Query(aes_encrypt('0'))):
    books = ["聊斋志异", "金瓶梅", "国色天香", "剪灯新话", "西游记", "三国演义", "水浒传","困于沙丘"]
    encrypt_books=[]
    for book in books:
        encrypt_books.append(aes_encrypt(book))
    # random_books = random.sample(books, k=4)
    # return random_books
    if len(page)<5:
        return {'msg':"ERROR"}
    page=int(aes_decrypt(page))
    if page >=0 and page<=3:
        return encrypt_books[page*2:page*2+2]
    else:
        return {'msg':"ERROR"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=5000)
```


