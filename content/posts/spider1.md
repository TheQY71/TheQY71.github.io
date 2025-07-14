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

## 5. 编码

### 1.5.1 URL编码 (URL Encoding)

在网络传输中，URL（统一资源定位符）中包含一些特殊字符（如空格、中文、某些符号等）时，可能会导致解析错误或歧义。为了确保URL的有效性和正确传输，需要对这些特殊字符进行编码，将其转换为一系列百分号（%）后跟两位十六进制数的表示形式。这个过程就是URL编码。

Python的 `urllib.parse` 模块提供了处理URL编码和解码的工具。

#### 示例：对中文字符进行URL编码

```python
import urllib.parse

# 待编码的字符串，这里是一个中文字符串
original_string = "测试"

# 使用 urllib.parse.quote() 函数进行URL编码
# 默认编码方式是 UTF-8，这也是Web开发中常用的编码
encoded_string = urllib.parse.quote(original_string)

print(f"原始字符串: {original_string}")
print(f"编码后字符串: {encoded_string}")
# 预期输出:
# 原始字符串: 测试
# 编码后字符串: %E6%B5%8B%E8%AF%95
```

**输出解释：**

- `%E6%B5%8B%E8%AF%95` 是 "测试" 两个字经过UTF-8编码后，再转换为URL编码的结果。
- `%E6%B5%8B` 代表汉字 "测" 的UTF-8编码（E6 B5 8B）。
- `%E8%AF%95` 代表汉字 "试" 的UTF-8编码（E8 AF 95）。

#### 扩展知识点：

- `urllib.parse.quote(string, safe='/', encoding=None, errors=None)`**:
  - `string`: 必需，要编码的字符串。
  - `safe`: 可选，一个字符串，包含不需要编码的字符。默认情况下，`/` 不会被编码。
  - `encoding`: 可选，指定编码方式，默认为 `UTF-8`。
- `urllib.parse.unquote(string, encoding='utf-8', errors='replace')`**:
  - 用于将URL编码的字符串解码回原始字符串。
  - 例如：`urllib.parse.unquote('%E6%B5%8B%E8%AF%95')` 将返回 `'测试'`。

URL编码是Web开发中处理GET请求参数、构建API请求等场景中非常基础且重要的操作。


### 1.5.2 Base64编码

#### base64基础
Base64是一种将二进制数据编码为ASCII字符串的编码方法，常用于在文本协议中传输二进制数据。它将每3个字节（24位）的数据编码为4个Base64字符（每个字符6位）。(ASCII编码不够6位截断的时候末尾补0)

[![2025-07-10-10-01-24.png](https://i.postimg.cc/QCtsPGt9/2025-07-10-10-01-24.png)](https://postimg.cc/JD99DFjM)

```python
import base64

s = "you"
s_base64 = base64.b64encode(s.encode())
print(s_base64)  # b'eW91'
```

#### base64替换
base64编码中没有`-`和`_`，这是网站对base64编码做了替换，现在我们要替换回去：

```python
data = s.replace("-", "+").replace("_", "/")
```

#### base64图片

```python
import base64
s = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAMAAABEpIrGAAAA7VBMVEUAAAD////////s8v////+txP+qwv+4zf/w9f/2+P+hu//Q3f+yyP+4zf/Q3f////+kvv+90P+80f+2yv/S4P/T4P/M2//z9/+cuP/V4P9Whv////9Uhf9Sg/9Pgf9NgP/8/f9di/9Xh/9lkf5aif9qlP7z9//k7P/c5v+2y/94nv51nP6lv/+LrP6Ep/6BpP5gjf7v9P+wxv/U4f/M2/+sxP+vxv73+f/P3v/J2v+5zf+ivP+fuv9xmf+Ytv6Usv6Hqf58of5vl/7m7v/g6f+zyf6QsP75+//q8P/B0v/W4//C1P6+0P6qwv6ct/76fHZiAAAAGnRSTlMAGAaVR/Py45aC9Mfy2b8t9OPZ2ce/v4L0x/e74/EAAAIZSURBVDjLZVPXYuIwEDSmQ4BLv5O0ku3Yhwu2IZTQe0hy7f8/57QSoYR5sVea1c424wgzl324LRRuH7I507hEJluYucCFEOBGhWzmy7X5+N0WwIjTbrcdBsKulM0z96onGCGE2X6n+cTkj/CqJ480igzkNXp26E9JkABSbBz8i4Bn3EkH840mKHoxs49fZQzt2Kd03FQEzSB3WsejB9Jqf1CJQBM0wCurABWBoub0gkDENwyStTHA62pwSWDtklRQ4FLfjnaiPqVW60hAYeLKNHIREOZuKTL80H6XBFCwn4BAmDOyLiOQUIlOSEjaoS+Ju57NZuul73Fml4w6yAivSLBW3MGfcfBmIegmArg3alICdJHgy1jQt8Z/6CcC4DdGXhLIoiWRACpbLYbDYW80GnXp2GH8ShP+PUvEoHsAIFq9Xm8+kXlIwkkI9pm+05Tm3yWqu9EiB0pkwjWBx2i+tND1XqeZqpPU4VhUbq/ekR8CwTRVoRxf3ifTbeIwcONNsJZ2lxFVKDMv1KNvS2zXdrnD+COvR1PQpTZKNlKD3cLCOJNnivgVxkw169BunlKFaV9/B+LQbqOsByY4IVgDB59dl/cjR9TIJV1Lh7CGqUqH/DDPhhZYOPkdLz6m0X7GrzPHsSe6zJwzxvm+5NeNi8U5ABfn7mz7zHJFrZ6+BY6rd7m8kQtcAtwwXzq4n69/vZbP1+pn6/8fsrRmHUhmpYYAAAAASUVORK5CYII="
base64_img = s.split(",")[1]  # 获取base64编码部分
img_bytes = base64.b64decode(base64_img)
with open("output_image.png", "wb") as img_file:
    img_file.write(img_bytes)
```

### 1.5.3 摘要算法(MD5, SHA1, SHA256, SHA512)


```python
import hashlib

data = b"Hello, World!"

# MD5
md5_hash = hashlib.md5(data).hexdigest()
print(f"MD5: {md5_hash}")
# MD5: 65a8e27d8879283831b664bd8b7f0ad4

# SHA1
sha1_hash = hashlib.sha1(data).hexdigest()
print(f"SHA1: {sha1_hash}")
# SHA1: 0a0a9f2a6772942557ab5355d76af442f8f65e01

# SHA256
sha256_hash = hashlib.sha256(data).hexdigest()
print(f"SHA256: {sha256_hash}")
# SHA256: dffd6021bb2bd5b0af676290809ec3a53191dd81c7f70a4b28688a362182986f

# SHA512
sha512_hash = hashlib.sha512(data).hexdigest()
print(f"SHA512: {sha512_hash}")
# SHA512: 374d794a95cdcfd8b35993185fef9ba368f160d8daf432d08ba9f1ed1e5abe6cc69291e0fa2fe0006a52570ef18c19def4e617c33ce52ef0a6e5fbe318cb0387
```

MD5哈希算法支持分块更新，多次update()等同于一次性对所有数据进行update()。
```python
from hashlib import md5

data = "123"
data1 = "456"
m = md5("IamSalt".encode())
m.update(data.encode())
m.update(data1.encode())
print(m.hexdigest())  # 501b685bc3b8c1cf80f9dc6f0bba1a24
print(md5(b"IamSalt123456").hexdigest())  # 501b685bc3b8c1cf80f9dc6f0bba1a24
```

`hexdigest()`返回十六进制字符串，`digest()`返回字节序列。

1.  **MD5 (Message-Digest Algorithm 5)**
    *   **特点**: 128位哈希值，速度快，但安全性低，易发生碰撞。
    *   **场景**: 文件完整性校验（已不推荐用于安全敏感场景）。
    *   **逆向**: 无法直接逆向，但可通过彩虹表、暴力破解等方式“碰撞”出原文。

2.  **SHA-1 (Secure Hash Algorithm 1)**
    *   **特点**: 160位哈希值，曾广泛使用，但安全性已不足，存在理论碰撞风险。
    *   **场景**: Git版本控制（文件校验），TLS/SSL证书（已弃用）。
    *   **逆向**: 同MD5，无法直接逆向，可通过碰撞攻击。

3.  **SHA-256 (Secure Hash Algorithm 256)**
    *   **特点**: 256位哈希值，SHA-2家族成员，安全性较高，目前广泛使用。
    *   **场景**: 区块链（比特币挖矿）、数字签名、密码存储（加盐后）。
    *   **逆向**: 无法直接逆向，计算复杂度极高，碰撞难度大。

4.  **SHA-512 (Secure Hash Algorithm 512)**
    *   **特点**: 512位哈希值，SHA-2家族成员，安全性更高，计算量相对较大。
    *   **场景**: 高安全性要求的数据完整性校验、密码存储（加盐后）。
    *   **逆向**: 无法直接逆向，计算复杂度极高，碰撞难度极大。

Web中的使用场景:

*   **密码存储**: 用户密码经过加盐（salt）后，使用SHA-256或SHA-512进行哈希存储，而非明文。
*   **数据完整性**: 校验下载文件、API响应数据的完整性，防止篡改。
*   **数字签名**: 确保数据来源的真实性和完整性，如JWT令牌签名。
*   **内容寻址**: CDN、IPFS等系统通过内容的哈希值来唯一标识和检索数据。

这些哈希算法（MD5, SHA-1, SHA-256, SHA-512）对任意长度的输入文本进行加密（哈希）后，得到的输出结果长度是固定且一致的。


## 6.加密

### 6.1 对称加密之AES
AES（高级加密标准）是一种对称密钥加密算法, 对称加密指的是加密和解密用的钥匙是一把, AES密钥长度：128、192或256位

CBC和ECB是AES的两种工作模式：

- ECB (Electronic Codebook)**：
  - 每个数据块独立加密。
  - 相同明文块会产生相同密文块。
  - 不安全，易受模式分析攻击。
- CBC (Cipher Block Chaining)**：
  - 每个数据块在加密前与前一个密文块进行异或操作。
  - 需要一个初始化向量（IV）。
  - 相同明文块会产生不同密文块。
  - 更安全，广泛使用。

#### ECB 
加密案例：
```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import base64

key = "0123456789abcdef".encode()  # 因为是aes-128，所以key长度必须是16字节
text = "alex is a monkey"  # 加密内容，长度必须是16的倍数
text = pad(text.encode(), 16)
print("pad text:", text)
# pad text: b'alex is a monkey\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10'

aes = AES.new(key=key, mode=AES.MODE_ECB)  # ECB模式不需要iv
en_text = aes.encrypt(text)
print("aes加密数据：：：", en_text)
# aes加密数据：：： b'8O\xc9JM[\xbb\x04\xb3MK\x1f\x07\x018\x117r"\xe0a\xa9$\xc5\x91\xcd\x9c\'\xea\x16>\xd4'

b64encode_encrypt_data = base64.b64encode(en_text)
print("base64加密数据：：：", b64encode_encrypt_data)
# base64加密数据：：： b'OE/JSk1buwSzTUsfBwE4ETdyIuBhqSTFkc2cJ+oWPtQ='
```
注意：先加密后编码，ECB模式不需要初始化向量(IV)

解密：
```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import base64

key = "0123456789abcdef".encode()  # 因为是aes-128，所以key长度必须是16字节
base64_encrypt_data = b"OE/JSk1buwSzTUsfBwE4ETdyIuBhqSTFkc2cJ+oWPtQ="
encrypt_data = base64.b64decode(base64_encrypt_data)
aes = AES.new(key=key, mode=AES.MODE_ECB)  # ECB模式不需要iv
de_text = aes.decrypt(encrypt_data)
de_text = unpad(de_text, AES.block_size).decode("utf-8")
print("aes解密数据：：：", de_text)
# aes解密数据：：： alex is a monkey
```

#### ECB与CBC模式的区别：
- **ECB模式**：电子密码本模式，每个明文块独立加密，相同的明文块会产生相同的密文块，不需要IV
- **CBC模式**：密码块链接模式，每个明文块与前一个密文块进行异或运算后再加密，需要IV作为第一个块的初始值
- **安全性**：CBC模式比ECB模式更安全，因为ECB模式会暴露明文的模式信息 


#### CBC 
加密案例：
```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import base64

key = "0123456789abcdef".encode()  # 因为是aes-128，所以key长度必须是16字节
iv = b"abcabcabcabcabca"  # 偏移量：因为是aes-128，所以iv长度必须是16字节
text = "alex is a monkey"  # 加密内容，长度必须是16的倍数

text = pad(text.encode(), 16)
print("pad text:", text)
# pad text: b'alex is a monkey\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10\x10'

aes = AES.new(key=key, mode=AES.MODE_CBC, iv=iv)
en_text = aes.encrypt(text)
print("aes加密数据：：：", en_text)
# aes加密数据：：： b's\xc7\x0c+\xbe\x06\x90\xe8]s\x00z[\xcb:B\xf0\x9b\x02\x0eb\xc79\xe7\x11\xc4\x9fs\xf4\x90cK'

b64encode_encrypt_data = base64.b64encode(en_text)
print("base64加密数据：：：", b64encode_encrypt_data)
# base64加密数据：：： b'c8cMK74GkOhdcwB6W8s6QvCbAg5ixznnEcSfc/SQY0s='
```
注意：先加密后编码

解密：
```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import base64


key = "0123456789abcdef".encode()  # 因为是aes-128，所以key长度必须是16字节
iv = b"abcabcabcabcabca"  # 偏移量：因为是aes-128，所以iv长度必须是16字节
text = "alex is a monkey"  # 加密内容，长度必须是16的倍数

base64_encrypt_data = b"c8cMK74GkOhdcwB6W8s6QvCbAg5ixznnEcSfc/SQY0s="
encrypt_data = base64.b64decode(base64_encrypt_data)

aes = AES.new(key=key, mode=AES.MODE_CBC, iv=iv)
de_text = aes.decrypt(encrypt_data)
de_text = unpad(de_text, AES.block_size).decode("utf-8")
print("aes解密数据：：：", de_text)
# aes解密数据：：： alex is a monkey
```

### 6.2 对称加密之DES

### 6.3 非对称加密RSA

公钥加密，私钥解密：数据加密
私钥加密，公钥解密：数字签名

#### 生成密钥
```python
from Crypto.PublicKey import RSA

# 构建ras算法对象
rsaKey = RSA.generate(1024)
# 获取公钥
publicKey = rsaKey.publickey().export_key()
# 获取私钥
privateKey = rsaKey.export_key()

with open("rsa.public.pem", "wb") as f:
    f.write(publicKey)

with open("rsa.private.pem", "wb") as f:
    f.write(privateKey)
```

#### 加密文本

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5
import base64

data = "alex is a monkey"

with open("./rsa.public.pem", "r") as f:
    # (1) 获取公钥对象
    public_key = RSA.import_key(f.read())

    # (2) 获取rsa算法对象
    rsa = PKCS1_v1_5.new(public_key)

    # (3) 基于RSA数据进行加密
    encrypted_data = rsa.encrypt(data.encode())
    print("encrypted_data: ", encrypted_data)
    # encrypted_data:  b'\x11E\xcf`4\xeeK\xe2\xafdwE\xb4\x92\xde"\xa9\x15\xd9\x97\x96:\xda{\xa4\xcfg[hd\x94\x8d %U\xb1\x10z\xb4\xd6:\xf2\x1e\xc8\x96\xbdt\x04\xed\x03c\x9f/?\xe6\x08?v\xa6;qhp\x197\xb3\xb2\xa8\xea\xb4T\xd5\xb7:\xae\xb8\xb4\xdb25\xda\x15d\x07w\x18P\xcd\x89\x17\xd4\xef\xd9\xa0d3@\x96@0\xae\xd0;.\xc1\'\xbe\xde\x7f\x89\xea\x10\x80H\x91\x92,\xaf\x99\xc0\xc8\x1d\xbb\xc4,qv\xc2'

    # (4) 为了能在网络中正确传输，进行base64编码
    base64_encrypted_data = base64.b64encode(encrypted_data)
    print("base64_encrypted_data: ", base64_encrypted_data.decode())
    # base64_encrypted_data:  EUXPYDTuS+KvZHdFtJLeIqkV2ZeWOtp7pM9nW2hklI0gJVWxEHq01jryHsiWvXQE7QNjny8/5gg/dqY7cWhwGTezsqjqtFTVtzquuLTbMjXaFWQHdxhQzYkX1O/ZoGQzQJZAMK7QOy7BJ77ef4nqEIBIkZIsr5nAyB27xCxxdsI=
```

#### 解密文本

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5
import base64

data = "EUXPYDTuS+KvZHdFtJLeIqkV2ZeWOtp7pM9nW2hklI0gJVWxEHq01jryHsiWvXQE7QNjny8/5gg/dqY7cWhwGTezsqjqtFTVtzquuLTbMjXaFWQHdxhQzYkX1O/ZoGQzQJZAMK7QOy7BJ77ef4nqEIBIkZIsr5nAyB27xCxxdsI="

with open("./rsa.private.pem", "r") as f:
    # (1) 获取公钥对象
    private_key = RSA.import_key(f.read())

    # (2) 获取rsa算法对象
    rsa = PKCS1_v1_5.new(private_key)

    # (3) 基于RSA数据进行解密
    decrypted_data = rsa.decrypt(base64.b64decode(data), None)
    print("decrypted_data: ", decrypted_data)
    # decrypted_data:  b'alex is a monkey'
```

## 7 js版本的加密算法

### 7.1 md5_hash

```javascript
const CryptoJS = require('crypto-js');
// 原始数据
const data = 'Hello, World!';
// 生成MD5摘要
const md5Digest = CryptoJS.MD5(data).toString();
console.log(md5Digest); // 65a8e27d8879283831b664bd8b7f0ad4
```

### 7.2 AES 加密

```javascript
const CryptoJS = require('crypto-js');
var key = CryptoJS.enc.Utf8.parse('1234567890123456'); // 16字节密钥
var iv = CryptoJS.enc.Utf8.parse('1234567890123456'); // 16字节IV
var plaintext = 'Hello, World!';
var ciphertext = CryptoJS.AES.encrypt(plaintext, key, {
    iv: iv,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7
}).toString();
console.log(ciphertext); // KQ8kBYRmIyMCoh9rwsq6YA==
```



