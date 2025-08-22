+++
date = '2025-07-08T13:31:44+08:00'
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

## 对象

### 原型链

```javascript
const Person = {
    get_name: function () {
        console.log(this);
        return this.name;
    },
    get_age: function () {
        console.log(this);
        return this.age;
    }
}

const xiaoming = {
    name: "小明",
    age: 18,
}

xiaoming.__proto__ = Person

console.log(xiaoming.get_name());
// { name: '小明', age: 18 }
// 小明
console.log(xiaoming.get_age());
// { name: '小明', age: 18 }
// 18

const get_name1 = xiaoming.get_name
console.log(get_name1());
// <ref *1> Object [global] {
//   global: [Circular *1],
//   clearImmediate: [Function: clearImmediate],
//   setImmediate: [Function: setImmediate] {
//     [Symbol(nodejs.util.promisify.custom)]: [Getter]
//   },
//   clearInterval: [Function: clearInterval],
//   clearTimeout: [Function: clearTimeout],
//   setInterval: [Function: setInterval],
//   setTimeout: [Function: setTimeout] {
//     [Symbol(nodejs.util.promisify.custom)]: [Getter]
//   },
//   queueMicrotask: [Function: queueMicrotask],
//   structuredClone: [Function: structuredClone],
//   atob: [Function: atob],
//   btoa: [Function: btoa],
//   performance: [Getter/Setter],
//   fetch: [Function: fetch],
//   navigator: [Getter],
//   crypto: [Getter]
// }

// undefined


const get_name1_ = Person.get_name
console.log(get_name1_ === Person.get_name)
// true

const get_name2 = xiaoming.get_name
console.log(get_name2());
// <ref *1> Object [global] {
//   global: [Circular *1],
//   clearImmediate: [Function: clearImmediate],
//   setImmediate: [Function: setImmediate] {
//     [Symbol(nodejs.util.promisify.custom)]: [Getter]
//   },
//   clearInterval: [Function: clearInterval],
//   clearTimeout: [Function: clearTimeout],
//   setInterval: [Function: setInterval],
//   setTimeout: [Function: setTimeout] {
//     [Symbol(nodejs.util.promisify.custom)]: [Getter]
//   },
//   queueMicrotask: [Function: queueMicrotask],
//   structuredClone: [Function: structuredClone],
//   atob: [Function: atob],
//   btoa: [Function: btoa],
//   performance: [Getter/Setter],
//   fetch: [Function: fetch],
//   navigator: [Getter],
//   crypto: [Getter]
// }

// undefined
```

[![2025-08-22-15-13-02.png](https://i.postimg.cc/6pfMTwzZ/2025-08-22-15-13-02.png)](https://postimg.cc/bZdxF77Y)