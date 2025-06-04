+++
date = '2025-06-04T13:31:44+08:00'
draft = false
title = '志远js逆向课程笔记'
categories = ["逆向"]
tags = ["js", "逆向", "python"]
+++

# 备忘

## 常见加密值
md5(123456,32) = e10adc3949ba59abbe56e057f20f883e

md5(123456,16) = 49ba59abbe56e057

# JS 基础

局部变量在函数执行完毕后销毁。
全局变量在页面关闭后销毁。

## 内部函数如何外部调用?

在 JavaScript 中，内部函数（即定义在另一个函数内部的函数）默认只能在其外部函数的作用域内访问，外部无法直接调用。但有几种常见方法可以让外部调用内部函数：

### 1. 返回内部函数（闭包）

你可以让外部函数返回内部函数，这样外部就能通过返回值调用它：

```js
function outer() {
  function inner() {
    console.log('内部函数被调用');
  }
  return inner; // 返回内部函数
}

const fn = outer(); // fn 现在就是 inner
fn(); // 输出：内部函数被调用
```

### 2. 将内部函数挂载到外部对象

你可以把内部函数赋值给外部对象的属性：

```js
function outer() {
  function inner() {
    console.log('内部函数被调用');
  }
  // 挂载到全局对象（不推荐，演示用）
  window.inner = inner;
}

outer(); // 没有输出，因为内部函数不会被调用
inner(); // 输出：内部函数被调用
```
> 注意：在 Node.js 环境下用 `global.inner = inner;`，在浏览器用 `window.inner = inner;`。

### 3. 通过变量传递

```js
var inner_;
function outer() {
  function inner() {
    console.log('内部函数被调用');
  }
  // 挂载到全局对象（不推荐，演示用）
  inner_ = inner;
}
outer();
inner_(); // 输出：内部函数被调用
```

### 4. 通过参数传递

你也可以把内部函数作为参数传递出去：

```js
function outer(callback) {
  function inner() {
    console.log('内部函数被调用');
  }
  callback(inner);
}

outer(function(fn) {
  fn(); // 输出：内部函数被调用
});
```


## 自执行函数

```javascript
// 方式1：最常用的写法
(function() {
    console.log('这是一个自执行函数');
})();

// 方式2：另一种写法
(function() {
    console.log('这是另一个自执行函数');
}());
```

```javascript
!function() {
    console.log('这是一个带!的自执行函数');
}();
```
立即执行：! 操作符会立即执行这个函数

补充：+-~这几个符号也会使自执行函数立即执行，但是处理返回值的方式不同，如果不需要返回值，使用 ! 操作符最简单。

--- 

## 浏览器环境

DOM相关知识: 
- [DOM HTML](https://www.runoob.com/js/js-htmldom-html.html)
- [DOM 事件](https://www.runoob.com/js/js-htmldom-events.html)
- [DOM 元素](https://www.runoob.com/js/js-htmldom-elements.html)
- [DOM EventListener](https://www.runoob.com/js/js-htmldom-eventlistener.html)

### 补头


用于过检测

```javascript
window = {
    location:{
        href:"chrome://new-tab-page/"
    }
}

window.location.href
```

### 例子

#### document.write+""

```javascript
document.write+""
```

浏览器和本地执行上面这行代码的结果不同，这个性质会被利用于检测。

浏览器：`'function write() { [native code] }'`
本地：`document is not defined`

这行代码实际上是运行`document.wirte.toString()`

检测逻辑：
```javascript
if (document.write + "" != 'function write() { [native code] }') {
    console.log("检测失败")
} else {
    console.log("检测成功")
}
```

所以可以使用hook的方法过这个检测:

```javascript
// hook document.write
document.write.toString = function () {
    return "function write() { [native code] }"
}
```


### js 检查浏览器环境

```javascript
navigator.plugins
```

### dom 事件

常见的DOM事件及其应用场景:

1. 点击事件(click)
   - 用户登录按钮点击
   - 表单提交
   - 页面交互操作

2. 页面加载事件(load/DOMContentLoaded) 
   - 收集浏览器指纹
   - 检测运行环境
   - 初始化页面数据

3. 图片加载事件(load) 
   - 滑块验证码图片处理
   - 图片预加载
   - 图片懒加载

4. 鼠标移动事件(mousemove)
   - 无感验证
   - 用户行为轨迹分析
   - 浏览器指纹采集

5. 表单事件
   - 输入框值变化(change)
   - 表单提交(submit) 
   - 数据验证

6. 键盘事件(keydown/keyup/keypress)
   - 快捷键
   - 输入监听
   - 按键记录
   
## 表单

除了js可以传数据，html中的表单也可以传数据
下面是一个简单的例子：
```javascript
<form action="https://httpbin.org/post" method="post">
    <input type="text" name="name" placeholder="name">
    <input type="text" name="age" placeholder="age">
    <input type="submit" value="submit">
</form>
```
注意：action可以是php文件，onsubmit可以执行js函数


# JS 调试

## Event Listeners

点登陆的按钮，elements>Event Listeners下会新出现这个按钮所绑定的事件(例如submit)

然后可以点击到对应的js源代码中

### 事件监听器调试

1. **查看事件绑定**：
   - 在浏览器开发者工具中，选择要调试的元素（例如登录按钮）。
   - 在 `Elements` 面板中，找到 `Event Listeners` 选项卡。
   - 点击该元素，查看绑定的事件（例如 `submit`）。

2. **定位源代码**：
   - 点击事件名称，可以直接跳转到对应的 JavaScript 源代码。
   - 这有助于快速定位和调试事件处理逻辑。

