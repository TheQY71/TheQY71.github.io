+++
date = '2025-09-11T10:35:26+08:00'
draft = false
title = 'Markdown_fold'
+++

我遇到长的代码块不想直接展示在博客中，想尝试折叠的方法，大模型给了我一种写法：

```html
<details>
<summary>点击查看运行结果</summary>
```code
    The code you wish to conceal.
    line2
\```（实际使用请删除前面这个\, 这个\是我后加的，避免渲染错误）
</details>
```
**展示效果**：
<details>
<summary>点击查看运行结果</summary>
```code
    The code you wish to conceal.
    line2
\```（实际使用请删除前面这个\）
</details>

---

可以看见折叠得一坨，爷👴的换行呢？

解决这个问题很简单:
1. 在 `</summary>` 标签的下一行增加一个空行。
2. 在代码块结束的 ``` 和 `</details>` 标签之间也增加一个空行（虽然这个空行在某些解析器中不是必须的，但加上更保险、规范）。

```html
<details>
<summary>点击查看运行结果</summary>

```code
    The code you wish to conceal.
    line2
    line3
    line4
    line5
\```（实际使用请删除前面这个\）

</details>
```

展示效果：
<details>
<summary>点击查看运行结果</summary>

```code
    The code you wish to conceal.
    line2
    line3
    line4
    line5
```

</details>

---
换行又回来了😊

但是使用typora写markdown的话，就会发现在typora中渲染的结果是这样的。：

[![image.png](https://i.postimg.cc/Bb6kM0MQ/image.png)](https://postimg.cc/7GFKZjFp)

当然如果typora只是作为编辑博客的工具，这种渲染结果可以忽略。强迫症患者请继续往下读～

这是由于不同的markdown平台对markdown的渲染严格程度不同：
- 宽松的解析器 (如 GitHub): 允许在 HTML 块标签（如`<details>`）内部通过空行切换回 Markdown 解析模式，实现 html > md 的嵌套。
- 严格的解析器 (typora): 一旦进入 HTML 块，就不再解析内部的 Markdown 块级语法（比如 ``` 代码块）。它会把所有内容都当作 HTML 来处理，直到遇到 HTML 块的闭合标签。这就是为什么您的代码块被“踢”了出来，而 </details> 被当成普通文本。

此时可以使用严格的HTML写法：
```html
<details>
<summary>点击查看运行结果</summary>
<pre><code>
The code you wish to conceal.
line2
</code></pre>
</details>
```

博客web页面的效果展示：
<details>
<summary>点击查看运行结果</summary>
<pre><code>
The code you wish to conceal.
line2
</code></pre>
</details>

---

typora中的效果展示：
[![image.png](https://i.postimg.cc/zXPJ9T2p/image.png)](https://postimg.cc/SJCF9Yhz)

over
