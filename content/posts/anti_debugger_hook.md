+++
date = '2026-07-21T14:18:31+08:00'
draft = false
title = 'Anti_debugger_hook'
+++

## Function 

### debugger

```javascript
(function() {
    return ![];
}['constructor']('debugger')['apply']('stateObject'));
```

等价于

```javascript
Function('debugger')
```

### hook 脚本

```javascript
(() => {
    const p = Object.getPrototypeOf(function () {});
    const raw = p.constructor;

    Object.defineProperty(p, 'constructor', {
        configurable: true,
        writable: true,
        value: function (...args) {
            const body = String(args.at(-1) ?? '').trim();
            return /^debugger;?$/.test(body)
                ? function () {}
                : Reflect.apply(raw, this, args);
        }
    });
})();
```

### 解释

这个 Hook 的目的就是：

> **把普通函数对象的 `.constructor` 替换掉；当有人用它动态创建 `debugger` 函数时，返回一个空函数；其他动态函数照常创建。**

#### 1. 外层立即执行函数

```js
(() => {
    // ...
})();
```

这是 IIFE(Immediately Invoked Function Expression)，定义后立即执行。作用是创建一个独立作用域，避免 `p`、`raw` 等变量污染全局环境。

---

#### 2. 找到普通函数真实使用的原型

```js
const p = Object.getPrototypeOf(function () {});
```

`function () {}` 创建了一个普通函数对象。

它的原型通常是：

```js
Function.prototype
```

所以正常情况下近似等价于：

```js
const p = Function.prototype;
```

但这里没有直接写 `Function.prototype`，而是从一个真实的函数字面量反向获取原型。

这样更可靠，因为目标代码也是从函数对象读取：

```js
(function () {}).constructor
```

它实际查找过程是：

```text
函数对象自身没有 constructor
        ↓
查找函数对象的原型 p
        ↓
读取 p.constructor
```

因此直接修改 `p.constructor`，能准确拦截目标调用路径。

---

#### 3. 保存原始构造器

```js
const raw = p.constructor;
```

修改前，`p.constructor` 是原始的 `Function` 构造器：

```js
raw === Function
```

保存它是为了让正常动态函数仍然能够执行。

例如：

```js
(function () {}).constructor('a', 'b', 'return a + b')
```

没有命中 `debugger` 时，仍交给 `raw` 创建：

```js
Function('a', 'b', 'return a + b')
```

如果不提前保存，Hook 内再次调用 `p.constructor`，就会调用 Hook 自己，造成无限递归。

---

#### 4. 重新定义 `constructor`

```js
Object.defineProperty(p, 'constructor', {
    configurable: true,
    writable: true,
    value: function (...args) {
        // ...
    }
});
```

这相当于把：

```js
p.constructor
```

从原始 `Function` 替换成自定义函数。

使用 `Object.defineProperty` 可以明确设置属性描述符：

```js
configurable: true
```

表示之后仍然可以再次修改或删除这个 Hook。

```js
writable: true
```

表示该属性之后仍然可以被赋值。

```js
value: function (...args) {}
```

是替换后的构造器函数。

---

#### 5. 接收 `Function` 构造器参数

```js
value: function (...args) {
```

`...args` 收集全部参数。

`Function` 构造器的调用形式是：

```js
Function(参数1, 参数2, ..., 函数体)
```

例如：

```js
Function('a', 'b', 'return a + b')
```

这里：

```js
args
```

是：

```js
[
    'a',
    'b',
    'return a + b'
]
```

最后一个参数永远被当作函数体。

对于目标代码：

```js
(function () {}).constructor('debugger')
```

`args` 是：

```js
['debugger']
```

---

#### 6. 提取并规范化函数体

```js
const body = String(args.at(-1) ?? '').trim();
```

拆开来看：

```js
args.at(-1)
```

获取最后一个参数，也就是动态函数的函数体。

```js
args.at(-1) ?? ''
```

如果不存在最后一个参数，使用空字符串。`??` 只在左边是 `null` 或 `undefined` 时启用右边。

```js
String(...)
```

把参数转换成字符串，因为 `Function` 构造器本来也会把参数字符串化。

```js
.trim()
```

删除函数体首尾的空白字符。

因此以下内容都会被整理：

```js
'debugger'
' debugger '
'\n debugger; \n'
```

分别得到：

```js
'debugger'
'debugger'
'debugger;'
```

---

#### 7. 判断是否为纯 `debugger` 函数体

```js
/^debugger;?$/.test(body)
```

这个正则表示：

```text
^           字符串开头
debugger    精确匹配 debugger
;?          分号出现 0 次或 1 次
$           字符串结尾
```

所以它匹配：

```js
debugger
debugger;
```

但不匹配：

```js
var a = 1; debugger;
debugger; return 1;
xxxdebugger
```

这是一个范围较窄的 Hook，只拦截函数体完全由 `debugger` 构成的动态函数，避免误伤包含正常逻辑的函数。

---

#### 8. 命中时返回空函数

```js
? function () {}
```

当函数体是 `debugger` 时，不调用原始 `Function`，而是返回：

```js
function () {}
```

所以原始反调试代码：

```js
(function () {})
    .constructor('debugger')
    .apply('stateObject');
```

被转换为：

```js
(function () {})
    .apply('stateObject');
```

这个空函数会被正常调用，但什么都不执行，也不会触发断点。

这里必须返回空函数，而不能直接：

```js
return;
```

因为目标代码后面还会调用：

```js
.apply(...)
```

返回 `undefined` 会导致：

```js
undefined.apply(...)
```

报 `TypeError`。

---

#### 9. 未命中时调用原始构造器

```js
: Reflect.apply(raw, this, args);
```

如果函数体不是纯 `debugger`，则调用保存的原始构造器。

近似等价于：

```js
raw.apply(this, args)
```

但 `Reflect.apply` 的语义更明确：

```js
Reflect.apply(
    要调用的函数,
    this 值,
    参数数组
)
```

例如：

```js
(function () {}).constructor(
    'a',
    'b',
    'return a + b'
);
```

最终会执行：

```js
Reflect.apply(
    raw,
    this,
    ['a', 'b', 'return a + b']
);
```

返回正常的动态函数。

---

#### 完整调用链

反调试代码：

```js
(function () {})
    ['constructor']('debugger')
    ['apply']('stateObject');
```

安装 Hook 后：

```text
(function () {})
    ↓
函数对象上没有自己的 constructor
    ↓
读取 p.constructor
    ↓
进入 Hook
    ↓
args = ['debugger']
    ↓
body = 'debugger'
    ↓
正则匹配成功
    ↓
返回 function () {}
    ↓
空函数.apply('stateObject')
    ↓
什么都不执行
```

而正常代码：

```js
(function () {}).constructor('return 123')()
```

执行路径是：

```text
body = 'return 123'
    ↓
正则不匹配
    ↓
Reflect.apply(raw, this, args)
    ↓
原始 Function('return 123')
    ↓
返回 123
```

这个 Hook 的关键不在于替换全局 `window.Function`，而在于直接替换目标表达式真正读取的：

```js
Object.getPrototypeOf(function () {}).constructor
```

