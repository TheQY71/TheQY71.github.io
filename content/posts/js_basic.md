+++
date = '2025-07-8T13:31:44+08:00'
draft = false
title = 'js基础'
categories = ["逆向"]
tags = ["js", "基础"]
+++

## 函数

### 匿名函数

```javascript
// 函数表达式
const func = function() { console.log('匿名函数'); };

// 立即执行函数表达式 (IIFE)
(function() { console.log('匿名函数'); })();

// 回调函数
setTimeout(function() { console.log('匿名函数'); }, 1000);
```

### 闭包

函数和对其周围状态（词法环境）的引用捆绑在一起的组合。

特点：
1.  **函数嵌套**：内部函数引用外部函数的变量。
2.  **外部函数返回内部函数**：使内部函数在外部函数执行完毕后仍能访问其作用域。

示例：
```javascript
function outer() {
  let count = 0;
  function inner() {
    count++;
    return count;
  }
  return inner;
}

const myCounter = outer();
console.log(myCounter()); // 1
console.log(myCounter()); // 2
```