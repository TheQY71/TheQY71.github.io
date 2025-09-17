+++
date = '2025-09-17T21:16:53+08:00'
draft = true
title = 'Learn_react'
+++

AI生成

# Vite 入门指南

这是一个简化的、更清晰的 Vite 项目初始化与启动流程。

## 1\. 创建项目

首先，在你的终端（Terminal）里运行以下命令来启动 Vite 的官方脚手架工具：

```bash
npm create vite@latest
```

这个命令会引导你完成几个关键步骤的设置：

  * **Project name:** 输入你的项目名称，例如 `my-vite-app`。
  * **Select a framework:** 选择你想要使用的前端框架，比如 `Vue`, `React`, `Svelte` 等。使用上下箭头进行选择，然后按回车确认。
  * **Select a variant:** 根据你选择的框架，进一步选择具体的变体。例如，如果你选择了 React，这里可以选择 `TypeScript` 或 `JavaScript`。

## 2\. 安装与启动

脚手架工具会为你生成项目的基本文件结构。现在，根据终端给出的提示，进入项目目录并完成后续步骤。

**第一步：进入项目目录**

```bash
cd <your-project-name>
```

> **提示**: 请将 `<your-project-name>` 替换成你第一步设置的实际项目名称。

**第二步：安装项目依赖**
这个命令会读取 `package.json` 文件，并下载所有项目运行所需的库和工具。

```bash
npm install
```

**第三步：启动开发服务器**
此命令会启动一个本地开发服务器，你可以在浏览器中实时预览你的应用。

```bash
npm run dev
```

启动成功后，终端通常会显示一个本地访问地址，默认为 `http://localhost:5173/`。在浏览器中打开这个地址，你就能看到你的 Vite 应用了！

## 初识React 

### `App.jsx`

**代码:**

```jsx
import Header from "./Header.jsx"
import Footer from "./Footer.jsx"
import Food from "./Food.jsx"

function App() {
  return (
    <>
      <Header />
      <Food />
      <Food />
      <Footer />
    </>
  );
}

export default App
```

**语法解析:**

  * **`import Header from "./Header.jsx"`**: 这是 JavaScript 的 **ES6 模块导入**语法。

      * `import ... from ...` 是导入其他模块（文件）中导出的变量、函数或组件的关键字。
      * `Header` 是我们为导入的组件指定的名称。因为 `Header.jsx` 使用了 `export default`，所以我们在这里可以自定义名称，但通常保持与原组件名一致。
      * `"./Header.jsx"` 是文件的相对路径。`./` 表示当前目录。Vite 等现代构建工具会根据这个路径找到并打包相应的文件。

  * **`function App() { ... }`**: 这是在 React 中定义一个**函数式组件 (Functional Component)** 的标准方式。

      * 它本质上是一个普通的 JavaScript 函数，但它返回 JSX，用于向屏幕上渲染 UI。
      * 组件的名称（这里是 `App`）按照约定俗成以大写字母开头。

  * **`return ( ... );`**: React 组件通过 `return` 语句来指定它要渲染的内容。

      * 使用圆括号 `()` 是为了包裹多行的 JSX 代码，这是一个推荐的最佳实践，可以避免 JavaScript 自动插入分号导致的问题。

  * **`<> ... </>`**: 这是 **React Fragment** 的简写语法。

      * **作用**：React 组件必须返回一个单一的根元素。如果你不想为了包裹多个元素而额外创建一个 `<div>`，就可以使用 Fragment。它能将子元素组合在一起，但本身不会在最终的 DOM 中创建任何额外的节点。

  * **`<Header />`**: 这是**使用（或称“渲染”）一个 React 组件**的语法。

      * 它看起来像 HTML 标签，但因为 `Header` 是一个大写字母开头的变量，React 和 JSX 会把它识别为一个组件，而不是一个普通的 HTML 标签。
      * `/>` 表示这是一个**自闭合标签**，因为我们没有在 `<Header>` 和 `</Header>` 之间嵌套任何子元素。

  * **`export default App`**: 这是 ES6 的**默认导出**语法。

      * 它将 `App` 组件作为当前模块的默认输出。这使得其他文件（例如 Vite 项目的入口文件 `main.jsx`）可以轻松地导入并使用它。每个文件只能有一个 `export default`。

-----

### `Footer.jsx`

**代码:**

```jsx
function Footer() {
    return (
        <footer>
            <p>&copy;{new Date().getFullYear()} My website.</p>
        </footer>
    );
}

export default Footer
```

**语法解析:**

  * **`{new Date().getFullYear()}`**: 这是 JSX 的核心特性之一：**嵌入 JavaScript 表达式**。
      * 花括号 `{}` 在 JSX 中被称为“插值”或“转义舱口”。它告诉 React：“不要把这里面的内容当作普通文本，而是把它当作 JavaScript 代码来执行，并把执行结果渲染出来。”
      * `new Date().getFullYear()` 是一个标准的 JavaScript 表达式，用于获取当前的四位数年份。因此，页面上会动态显示当前的年份。

-----

### `Header.jsx`

**代码:**

```jsx
function Header() {
    return (
        <header>
            <h1>My website</h1>
            <nav>
                <ul>
                    <li><a href="#">Home</a></li>
                    <li><a href="#">About</a></li>
                    <li><a href="#">Services</a></li>
                    <li><a href="#">Contact</a></li>
                </ul>
            </nav>
            <hr />
        </header>
    );
}
export default Header;
```

-----

### `Food.jsx`

**代码:**

```jsx
function Food() {
    const food1 = "Orange"
    const food2 = "Bread"
    return (
        <ul>
            <li>Apple</li>
            <li>{food1}</li>
            <li>{food2.toUpperCase()}</li>
        </ul>
    );
}
export default Food
```