+++
date = '2025-08-30T20:25:06+08:00'
draft = false
title = 'Learn_html'
categories = ["前端"]
tags = ["html", "基础","学习笔记"]
+++

## 块级元素和行内元素


### 块级元素：

*   独占一行，宽度默认是父容器的100%。
*   可设置宽高、内外边距。
*   **可包含行内元素和块级元素。**
*   常见：`div`, `p`, `h1-h6`, `ul`, `li`。

### 行内元素：

*   与其他行内元素共享一行。
*   宽度由内容决定，不可设置宽高。
*   垂直方向的内外边距无效，水平方向有效。
*   **可包含行内元素，但不可包含块级元素。**
*   常见：`span`, `a`, `img`, `strong`, `em`。

## 超链接

### 跳转锚点

1.  **设置锚点：**
    *   在目标位置的元素上添加 `id` 属性。
    *   示例：`<h2 id="section1">第一部分</h2>`

2.  **创建链接：**
    *   使用 `<a>` 标签的 `href` 属性指向锚点 `id`。
    *   示例：`<a href="#section1">跳转到第一部分</a>`

3.  **跨页面锚点：**
    *   在 `href` 中指定文件路径后跟 `#` 和锚点 `id`。
    *   示例：`<a href="another_page.html#section2">跳转到另一页的第二部分</a>`
    

## HTML全局属性

*   `accesskey`: 设置元素的键盘快捷键。
*   `class`: 为元素指定一个或多个类名（CSS或JS使用）。
*   `contenteditable`: 允许用户编辑元素内容。
*   `data-*`: 用于存储自定义数据。
*   `dir`: 设置元素内容的文本方向（`ltr`或`rtl`）。
*   `draggable`: 允许元素被拖动。
*   `hidden`: 隐藏元素。
*   `id`: 为元素指定唯一的标识符。
*   `lang`: 设置元素内容的语言。
*   `spellcheck`: 检查元素内容的拼写和语法。
*   `style`: 为元素应用内联CSS样式。
*   `tabindex`: 设置元素的tab键顺序。
*   `title`: 提供元素的额外信息（鼠标悬停时显示）。
*   `translate`: 指定元素内容是否应被翻译。


## `<meta>` 标签信息

1.  **字符编码：**
    *   `<meta charset="utf-8">`

2.  **IE浏览器兼容性：**
    *   `<meta http-equiv="X-UA-Compatible" content="IE=edge">`

3.  **移动端视口配置：**
    *   `<meta name="viewport" content="width=device-width, initial-scale=1.0">`

4.  **网页关键词：**
    *   `<meta name="keywords" content="8-12个以英文逗号隔开的单词/词语">`

5.  **网页描述信息：**
    *   `<meta name="description" content="80字以内的一段话，与网站内容相关">`

6.  **搜索引擎爬虫配置 (`robots`)：**

    | 值          | 描述                               |
    | :---------- | :--------------------------------- |
    | `index`     | 允许搜索引擎索引此页面。           |
    | `noindex`   | 要求搜索引擎不索引此页面。         |
    | `follow`    | 允许搜索引擎跟随此页面上的链接。   |
    | `nofollow`  | 要求搜索引擎不跟随此页面上的链接。 |
    | `all`       | 与 `index, follow` 等价            |
    | `none`      | 与 `noindex, nofollow` 等价        |
    | `noarchive` | 要求搜索引擎不缓存页面内容。       |
    | `nocache`   | `noarchive` 的替代名称。           |

    *   示例：`<meta name="robots" content="index,follow">`

7.  **网页作者：**
    *   `<meta name="author" content="tony">`

8.  **网页生成工具：**
    *   `<meta name="generator" content="Visual Studio Code">`

9.  **定义网页版权信息：**
    *   `<meta name="copyright" content="2023-2027e版权所有">`

10. **配置网页自动刷新/跳转：**
    *   `<meta http-equiv="refresh" content="10;url=http://www.baidu.com">` (10秒后跳转到百度)