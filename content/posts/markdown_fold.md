+++
date = '2025-09-11T10:35:26+08:00'
draft = false
title = 'Markdown_fold'
+++

## 折叠代码块实现

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

## typora渲染折叠代码块错误

使用typora写markdown的话，就会发现在typora中渲染的结果是这样的。：

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


## hugo Shortcode

上面博客中折叠的代码没有行号，通过hugo shrotcode可以解决这个问题

**当我们在 Markdown 文件中直接写入 HTML 标签（如 `<details>`）时，Hugo 的 Markdown 处理器（默认是 Goldmark）会把这部分内容当作“原生 HTML 块”，并且默认不会再对这里面的 Markdown 内容应用 Hugo 特有的高级功能，比如代码高亮配置（包括行号、高亮行等）。**

要解决这个问题，你需要用“Hugo 的方式”来生成这个折叠效果。最佳实践是创建一个 **Shortcode**。

### 方法一：创建 Hugo Shortcode（官方推荐的最佳实践）

Shortcode 是 Hugo 的一个核心功能，它允许你创建可重用的 HTML 模板片段，并且能确保包裹在其中的 Markdown 内容被 Hugo 正确处理。

#### 步骤 1：创建 `details.html` Shortcode 文件

在你的 Hugo 项目的根目录下，找到或创建 `layouts/shortcodes/` 文件夹，然后在里面创建一个名为 `details.html` 的文件。

文件路径：`/layouts/shortcodes/details.html`

文件内容如下：

```html
<details>
  <summary>{{ .Get "summary" | default "点击查看详情" }}</summary>
  {{ .Inner | markdownify }}
</details>
```

**代码解释:**

  * `{{ .Get "summary" }}`: 获取你在 shortcode 中传入的名为 `summary` 的参数，作为折叠框的标题。
  * `| default "点击查看详情"`: 如果没有提供 `summary` 参数，就使用一个默认标题。
  * `{{ .Inner }}`: 这是最关键的部分，它代表所有包裹在 shortcode 开始和结束标签之间的内容。
  * `| markdownify`: 这是“魔法”所在！它告诉 Hugo：“请用标准的 Markdown 处理器来渲染 `Inner` 里的所有内容”。这样一来，代码块的行号、高亮等功能就都能生效了。

#### 步骤 2：在 Markdown 文件中使用 Shortcode

现在，你可以用下面这种更简洁、更强大的方式来写你的折叠代码块了。

**用法示例:**

```markdown
{{</* details summary="点击查看运行结果" */>}}
```text {hl_lines=["2"]}
The code you wish to conceal.
line2
line3
\```
{{\</\* /details \*/\>}}
```

**注意：**
* 上面的使用示例中的`/*`和`*\` 都要删掉，这是为了博客不渲染这块代码而额外加的。
* 现在你可以在代码块的语言标记后面自由地添加 Hugo 的所有代码块参数了，比如 `{linenos=true}` 就是显示行号。你还可以用 `{hl_lines=["2-3", 5]}` 来高亮特定行。

---

**实际效果**：
{{<details summary="点击查看代码">}}
```text {hl_lines=["2"]}
The code you wish to conceal.
line2
line3
```
{{</details>}}