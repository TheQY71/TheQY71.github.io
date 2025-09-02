+++
date = '2025-09-02T16:10:03+08:00'
draft = false
title = 'Learn_css'
categories = ["前端"]
tags = ["css", "基础","学习笔记"]
+++

# 基础

## 样式的优先级

```css
h1 {
    color: blue;
}
```

```html
<!DOCTYPE html>
<html lang="zh-CN">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS 优先级示例</title>

    <link rel="stylesheet" href="style.css">

    <style>
        h1 {
            color: orange;
        }
    </style>

</head>

<body>

    <h1>CSS 样式优先级演示</h1>

    <h1 style="color: red;">这个标题将显示为红色</h1>


    <hr>
    <h2>优先级总结：</h2>
    <ul>
        <li><strong>外部样式 (style.css)</strong> 尝试将标题变为 <code>blue</code> (蓝色)。</li>
        <li><strong>内部样式 (&lt;style&gt; 标签)</strong> 覆盖了外部样式，尝试将标题变为 <code>orange</code> (橙色)。</li>
        <li><strong>行内样式 (style 属性)</strong> 拥有最高优先级，最终将标题设置为 <code>red</code> (红色)。</li>
    </ul>

</body>

</html>
```

[![2025-09-02-16-14-49.png](https://i.postimg.cc/hjm6cf2X/2025-09-02-16-14-49.png)](https://postimg.cc/0M8cC2Rv)

## 选择器
`!important` > 行内样式 > ID选择器 > 类选择器 > 元素选择器 > * > 继承的样式

### 基本选择器

| 基本选择器 | 特点                                                  | 用法                  |
| :--------- | :---------------------------------------------------- | :-------------------- |
| 通配选择器 | 选中所有标签，一般用于清除样式。                      | `* {color:red}`       |
| 元素选择器 | 选中所有同种标签，但是不能差异化选择。                | `h1 {color:red}`      |
| 类选择器   | 选中所有特定类名（class 值）的元素 ——— 使用频率很高。 | `.say {color:red}`    |
| ID 选择器  | 选中特定 id 值的那个元素（唯一的）。                  | `#earthy {color:red}` |

### 复合选择器

CSS 复合选择器（Compound Selectors）是由两个或多个基本选择器组合而成的，它能让你根据元素之间的关系（如父子、兄弟）来更精确地定位元素。主要分为 **组合器** 和 **分组选择器** 两大类。

#### 一、组合器（Combinators）

组合器是用来描述两个选择器之间关系的特殊符号。

##### 1. 后代组合器 (Descendant Combinator)

  * **语法符号**：空格 ( )
  * **规则**：`选择器A 选择器B`
  * **作用**：选中 `选择器A` 元素内部的 **所有** 后代元素（儿子、孙子、重孙子...）中，符合 `选择器B` 的元素。
  * **示例**：
    ```css
    /* 选中 <div class="content"> 内部所有的 <p> 元素 */
    .content p {
      color: #333;
    }
    ```

##### 2. 子代组合器 (Child Combinator)

  * **语法符号**：大于号 (`>`)
  * **规则**：`选择器A > 选择器B`
  * **作用**：选中 `选择器A` 元素的 **直接子元素**（亲儿子）中，符合 `选择器B` 的元素。只深入一层。
  * **示例**：
    ```css
    /* 仅选中 <ul> 元素的直接子元素 <li> */
    ul > li {
      list-style-type: none;
    }
    ```

##### 3. 相邻兄弟组合器 (Adjacent Sibling Combinator)

  * **语法符号**：加号 (`+`)
  * **规则**：`选择器A + 选择器B`
  * **作用**：选中与 `选择器A` **紧邻其后** 的那 **一个** 兄弟元素，且该元素符合 `选择器B`。它们必须拥有相同的父元素。
  * **示例**：
    ```css
    /* 选中 <h2> 标题后面紧跟着的第一个 <p> 元素 */
    h2 + p {
      margin-top: 0;
    }
    ```

##### 4. 通用兄弟组合器 (General Sibling Combinator)

  * **语法符号**：波浪号 (`~`)
  * **规则**：`选择器A ~ 选择器B`
  * **作用**：选中在 `选择器A` **之后** 的 **所有** 兄弟元素中，符合 `选择器B` 的元素。它们也必须拥有相同的父元素。
  * **示例**：
    ```css
    /* 选中 <h2> 标题后面的所有兄弟 <p> 元素 */
    h2 ~ p {
      text-indent: 2em;
    }
    ```

#### 二、交集与并集选择器

##### 5. 交集选择器 (Intersection Selector)

  * **语法规则**：将多个基本选择器 **紧挨着写**，没有任何连接符号。
  * **规则**：`选择器A选择器B`
  * **作用**：同时满足多个条件的 **同一个元素**。最常见的是 "标签选择器 + 类/ID选择器"。
  * **示例**：
    ```css
    /* 选中 既是 <p> 标签，又拥有 .warning 类的那个元素 */
    p.warning {
      color: red;
      font-weight: bold;
    }

    /* 选中 既是 <div> 标签，又拥有 id="main" 的那个元素 */
    div#main {
      width: 960px;
    }
    ```

##### 6. 并集选择器 / 分组选择器 (Grouping Selector)

  * **语法符号**：逗号 (`,`)
  * **规则**：`选择器A, 选择器B, 选择器C`
  * **作用**：将相同的样式规则应用于多个不同的选择器，以减少代码冗余。
  * **示例**：
    ```css
    /* 同时为 h1, h2, h3 元素设置相同的字体和颜色 */
    h1, h2, h3 {
      font-family: 'Helvetica', sans-serif;
      color: #1a1a1a;
    }
    ```

#### 总结表格

| 类型               | 语法符号 | 语法示例    | 描述                                                |
| :----------------- | :------- | :---------- | :-------------------------------------------------- |
| **后代组合器**     | 空格     | `.nav p`    | 选中 `.nav` 内的**所有**后代 `<p>` 元素             |
| **子代组合器**     | `>`      | `ul > li`   | 选中 `<ul>` 的**直接子元素** `<li>`                 |
| **相邻兄弟组合器** | `+`      | `h1 + p`    | 选中 `<h1>` **紧邻之后**的那个 `<p>` 兄弟元素       |
| **通用兄弟组合器** | `~`      | `h1 ~ p`    | 选中 `<h1>` **之后所有**的 `<p>` 兄弟元素           |
| **交集选择器**     | (无符号) | `p.intro`   | 选中**同时**是 `<p>` 元素且 `class="intro"` 的元素  |
| **并集选择器**     | `,`      | `h1, .lead` | **同时**选中所有 `<h1>` 元素**和**所有 `.lead` 元素 |

### 伪类选择器
#### 什么是伪类选择器？

伪类选择器是 CSS 选择器的一种，它不基于元素的名称、ID 或类，而是**基于元素在特定时间的状态或其在文档树中的位置**来应用样式。简单来说，它为你提供了一种“虚拟的类”，让你可以在某些特殊情况发生时（如鼠标悬停、链接被访问过）才添加样式。

伪类选择器的语法是在选择器后面加上一个冒号 (`:`) 和伪类的名称。

例如：`a:hover` 会在鼠标指针悬停在 `<a>` 链接上时应用样式。

下面我们将常见的伪类选择器分为几大类进行总结。

---

#### 1. 动态与用户行为伪类

这类伪类与用户和元素的交互相关，非常普遍。

| 伪类                | 描述                                                                           | 示例                                                        |
| :------------------ | :----------------------------------------------------------------------------- | :---------------------------------------------------------- |
| **`:link`**         | 选择所有**未被访问过**的链接。                                                 | `a:link { color: blue; }`                                   |
| **`:visited`**      | 选择所有**已被用户访问过**的链接。                                             | `a:visited { color: purple; }`                              |
| **`:hover`**        | 选择鼠标指针**正悬停在上面**的元素。                                           | `button:hover { background-color: #eee; }`                  |
| **`:active`**       | 选择**被用户激活**（例如，点击鼠标按住不放）的元素。                           | `a:active { color: red; }`                                  |
| **`:focus`**        | 选择**获得焦点**的元素，通常用于 `<input>`, `<textarea>`, `<a>` 等可交互元素。 | `input:focus { border-color: orange; }`                     |
| **`:focus-within`** | 选择自身获得焦点，或其**某个后代元素获得焦点**的元素。                         | `div:focus-within { box-shadow: 0 0 5px rgba(0,0,0,0.2); }` |

**注意顺序**：在同时为链接定义这几个伪类时，为了保证样式正确生效，建议遵循 **LVHA** 顺序：`:link` — `:visited` — `:hover` — `:active`。

---

#### 2. 结构性伪类

这类伪类根据元素在文档树中的位置关系来选择元素，非常强大，可以实现很多复杂的布局选择，而无需添加额外的类。

| 伪类                     | 描述                                                                                              | 示例                                                |
| :----------------------- | :------------------------------------------------------------------------------------------------ | :-------------------------------------------------- |
| **`:root`**              | 选择文档的根元素。在 HTML 中，它就是 `<html>` 元素。常用于定义全局 CSS 变量。                     | `:root { --main-color: #333; }`                     |
| **`:first-child`**       | 选择作为其父元素的**第一个子元素**的元素。                                                        | `li:first-child { font-weight: bold; }`             |
| **`:last-child`**        | 选择作为其父元素的**最后一个子元素**的元素。                                                      | `p:last-child { margin-bottom: 0; }`                |
| **`:only-child`**        | 选择作为其父元素的**唯一子元素**的元素。                                                          | `div p:only-child { color: green; }`                |
| **`:nth-child(n)`**      | 选择作为其父元素的**第 n 个子元素**。`n` 可以是数字、关键字（`odd`, `even`）或公式（如 `2n+1`）。 | `tr:nth-child(even) { background-color: #f2f2f2; }` |
| **`:nth-last-child(n)`** | 与 `:nth-child` 类似，但从**最后一个子元素开始倒数**计算。                                        | `li:nth-last-child(2) { color: grey; }`             |
| **`:first-of-type`**     | 选择在其父元素中，**同类型**（同标签名）的第一个兄弟元素。                                        | `h2:first-of-type { color: darkred; }`              |
| **`:last-of-type`**      | 选择在其父元素中，**同类型**的最后一个兄弟元素。                                                  | `p:last-of-type { font-style: italic; }`            |
| **`:only-of-type`**      | 选择在其父元素中，**没有其他同类型**兄弟元素的元素。                                              | `span:only-of-type { padding: 5px; }`               |
| **`:nth-of-type(n)`**    | 选择在其父元素中，**同类型**的第 n 个兄弟元素。                                                   | `p:nth-of-type(2n) { color: navy; }`                |
| **`:empty`**             | 选择**内部没有任何子元素或文本内容**（包括空格）的元素。                                          | `div:empty { border: 1px dashed #ccc; }`            |

**`:nth-child` vs `:nth-of-type` 的区别**:
* `:nth-child(n)` 首先在所有兄弟元素中找到第 n 个孩子，然后检查它是否匹配你指定的选择器。
* `:nth-of-type(n)` 首先筛选出所有指定类型的元素，然后从这个子集中找到第 n 个。

---

#### 3. 表单与输入伪类

这类伪类专门用于根据表单控件的状态来选择它们。

| 伪类                | 描述                                                                                          | 示例                                                       |
| :------------------ | :-------------------------------------------------------------------------------------------- | :--------------------------------------------------------- |
| **`:checked`**      | 选择处于**选中状态**的 `<input type="radio">`, `<input type="checkbox">` 或 `<option>` 元素。 | `input[type="radio"]:checked + label { color: green; }`    |
| **`:disabled`**     | 选择被**禁用** (`disabled` 属性) 的表单元素。                                                 | `input:disabled { background-color: #f5f5f5; }`            |
| **`:enabled`**      | 选择**未被禁用**（可用）的表单元素。                                                          | `button:enabled { cursor: pointer; }`                      |
| **`:required`**     | 选择设置了 `required` 属性的表单元素。                                                        | `input:required { border-left: 3px solid red; }`           |
| **`:optional`**     | 选择没有 `required` 属性的表单元素。                                                          | `input:optional { border-left: 3px solid green; }`         |
| **`:read-only`**    | 选择设置了 `readonly` 属性的元素。                                                            | `input:read-only { background-color: #eee; }`              |
| **`:in-range`**     | 选择值在 `min` 和 `max` 属性规定范围内的 `<input>` 元素。                                     | `input[type="number"]:in-range { border-color: green; }`   |
| **`:out-of-range`** | 选择值超出 `min` 和 `max` 属性规定范围的 `<input>` 元素。                                     | `input[type="number"]:out-of-range { border-color: red; }` |
| **`:valid`**        | 选择值通过验证的表单元素。                                                                    | `input:valid { border-color: lightgreen; }`                |
| **`:invalid`**      | 选择值未通过验证的表单元素。                                                                  | `input:invalid { border-color: pink; }`                    |

---

#### 4. 其他伪类

| 伪类                 | 描述                                                             | 示例                                                |
| :------------------- | :--------------------------------------------------------------- | :-------------------------------------------------- |
| **`:target`**        | 选择当前 URL 的片段标识符（URL 中 `#` 后面的部分）所指向的元素。 | `section:target { background-color: lightyellow; }` |
| **`:not(selector)`** | 否定伪类。选择**不匹配**括号内选择器的所有元素。                 | `p:not(.special) { font-size: 14px; }`              |

掌握伪类选择器是编写高效、简洁 CSS 的关键。它极大地增强了 CSS 的能力，让你能够创建出富有交互性且结构清晰的网页样式。