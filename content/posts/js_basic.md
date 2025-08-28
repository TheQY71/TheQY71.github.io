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

**1. 是什么？**
* 每个 JS 对象都有一个“原型”对象（可以理解为它的“父亲”）。
* 这个“原型”对象自己也是一个对象，所以它也有自己的“原型”（即“爷爷”）。
* 这样一层层链接起来，直到顶层 `null` 为止，形成的链式结构就是**原型链**。

**2. 怎么工作？（查找规则）**
* 当读取对象的属性时（如 `obj.x`），JS 会先在**对象自身**上查找。
* 如果找不到，就会沿着原型链**向上**，去它的“原型”对象里查找。
* 重复此过程，直到找到该属性或到达链的终点 `null`。如果到终点还没找到，则返回 `undefined`。

**3. 为什么要有它？（核心用途）**
* **共享方法，节省内存**：将通用的方法（如 `toString`, `eat`）定义在原型上，所有实例对象都可以通过原型链访问到它，而无需在自己内部复制一份。
* **实现继承**：子对象可以继承父对象原型上的属性和方法，这是 JS 继承的基础。

**简单比喻：**
把它想象成一个“家族查找”的过程。找东西先看自己有没有，没有就问爸爸，爸爸没有就问爷爷，直到找到老祖宗为止。

### `this` 在原型方法中的指向

**1. 基本规则**
函数内部 `this` 的值，取决于该函数被调用时的上下文。当一个原型方法被对象实例调用时，方法内部的 `this` 就指向该对象实例。

**2. `this` 的确定**
`this` 在运行时确定，指向直接调用该函数的对象。

  * 对于 `instance.method()` 这种调用形式，`method` 内部的 `this` 就是 `instance`。
  * 方法虽然定义在原型上，但执行时的上下文由调用者实例提供。

**3. 代码示例**

```javascript
// 构造函数
function Animal(name, age) {
  // 实例属性
  this.name = name;
  this.age = age;
}

// 在原型上定义共享方法
Animal.prototype.introduce = function() {
  // this 指向调用本函数的具体实例
  console.log(`我是 ${this.name}，${this.age} 岁。`);
};

// 创建实例
const cat = new Animal('咪咪', 3);
const dog = new Animal('旺财', 5);

// 调用演示
// 1. cat 调用 introduce()
//    - 调用者是 cat，方法内的 this 指向 cat
cat.introduce(); // 输出: 我是 咪咪，3 岁。

// 2. dog 调用 introduce()
//    - 调用者是 dog，方法内的 this 指向 dog
dog.introduce(); // 输出: 我是 旺财，5 岁。
```

[![2025-08-25-13-05-15.png](https://i.postimg.cc/VsCKDk5X/2025-08-25-13-05-15.png)](https://postimg.cc/NLQ80Y60)

**4. 核心摘要**

  * **动态绑定**：`this` 在函数执行时才被确定，而非定义时。
  * **调用者**：`this` 指向调用函数的那个对象。
  * **资源共享**：所有实例共享同一个原型方法，但该方法可以通过 `this` 操作每个实例独有的数据。
  
  好的，我们来看箭头函数中的 `this`。它与普通函数截然不同。

-----

### 箭头函数中的 `this`

#### **1. 核心规则：没有自己的 `this`**

箭头函数本身**不创建**或**绑定**自己的 `this`。它根本没有自己的 `this` 上下文。

#### **2. `this` 的来源：词法作用域 (Lexical Scoping)**

  * 箭头函数会**捕获**其定义时所在**外层作用域**的 `this` 值。
  * 可以理解为，箭头函数内部的 `this` 和它外部的 `this` 是同一个。
  * 这个 `this` 的指向在函数定义时就已经确定，之后**永远不会改变**，即使使用 `.call()`, `.apply()`, `.bind()` 也无法修改。

#### **3. 代码示例：与普通函数的对比**

这个例子展示了在 `setTimeout` 回调中，普通函数和箭头函数的 `this` 指向差异。

```javascript
// 定义一个对象
const person = {
  name: '张三',
  
  // 使用普通函数作为方法
  sayNameLater_Regular: function() {
    // 此时，这里的 `this` 指向 person 对象
    console.log('方法内的 this:', this.name); // 输出: 方法内的 this: 张三

    setTimeout(function() {
      // 问题：这个函数由 setTimeout 调用，它的 this 指向全局对象 (window) 或在严格模式下是 undefined
      // 因此 this.name 会失败
      console.log('普通函数回调:', this.name); // 输出: 普通函数回调: undefined (或 window.name)
    }, 500);
  },

  // 使用箭头函数作为方法
  sayNameLater_Arrow: function() {
    // 此时，这里的 `this` 同样指向 person 对象
    console.log('方法内的 this:', this.name); // 输出: 方法内的 this: 张三

    setTimeout(() => {
      // 关键：箭头函数没有自己的 this，它会“借用”外层作用域（即 sayNameLater_Arrow 方法）的 this。
      // 外层的 this 指向 person，所以这里的 this 也指向 person。
      console.log('箭头函数回调:', this.name); // 输出: 箭头函数回调: 张三
    }, 1000);
  }
};

person.sayNameLater_Regular();
person.sayNameLater_Arrow();
```

#### **4. 使用场景总结**

**推荐使用**：

当你需要在回调函数（如 `setTimeout`, `map`, `forEach`）中**维持外部的 `this` 上下文**时。箭头函数解决了传统 `var self = this;` 的变通写法。

**避免使用**：

1.  **作为对象的方法**：

    ```javascript
    const obj = {
      name: 'My Object',
      // 错误用法：这里的 this 会指向全局 window 对象
      greet: () => console.log('Hello', this.name) 
    };
    obj.greet(); // 输出 'Hello ' (或 window.name 的值)
    ```

2.  **作为原型方法**：

    ```javascript
    function Animal() {}
    // 错误用法：this 会固定为定义时的全局 window 对象，而不是未来的实例
    Animal.prototype.eat = () => { console.log(this); }; 
    ```

    在这种场景下，你需要一个由调用者决定的动态 `this`，所以必须使用普通函数。

3.  **作为构造函数**：箭头函数不能用作构造函数，使用 `new` 会报错。

### `call`, `apply`, `bind` 用法

这三个都是 JavaScript 函数原型上的方法 (`Function.prototype`)，核心作用是**改变函数执行时的 `this` 上下文**，并执行函数。

#### 1. `Function.prototype.call()`

  * **作用**：立即调用函数，并手动指定其内部的 `this` 指向。

  * **语法**：`func.call(thisArg, arg1, arg2, ...)`

  * **参数**：

      * `thisArg`：函数执行时内部 `this` 的值。如果为 `null` 或 `undefined`，则 `this` 指向全局对象（浏览器中为 `window`）。
      * `arg1, arg2, ...`：传递给函数的参数，以**逗号分隔**的形式逐个传入。

  * **返回值**：被调用函数的返回值。

  * **代码示例**：

    ```javascript
    const person = {
      name: 'Alice'
    };

    function greet(greeting, punctuation) {
      console.log(`${greeting}, my name is ${this.name}${punctuation}`);
    }

    // 使用 call 调用 greet 函数，并将其 this 绑定到 person 对象
    greet.call(person, 'Hello', '!'); // 输出: Hello, my name is Alice!
    ```

#### 2. `Function.prototype.apply()`

  * **作用**：与 `call` 作用几乎完全相同，也是立即调用函数并指定 `this`。

  * **语法**：`func.apply(thisArg, [argsArray])`

  * **参数**：

      * `thisArg`：与 `call` 相同。
      * `[argsArray]`：一个**数组**或类数组对象，其元素将作为参数传递给函数。

  * **返回值**：被调用函数的返回值。

  * **代码示例**：

    ```javascript
    const person = {
      name: 'Bob'
    };

    function greet(greeting, punctuation) {
      console.log(`${greeting}, my name is ${this.name}${punctuation}`);
    }

    const args = ['Hi', '.'];

    // 使用 apply 调用 greet，参数通过数组传入
    greet.apply(person, args); // 输出: Hi, my name is Bob.
    ```

      * **巧用**: `apply` 可用于处理参数数组，例如求数组中的最大值。
        ```javascript
        const numbers = [5, 2, 8, 1, 9];
        const max = Math.max.apply(null, numbers); // 相当于 Math.max(5, 2, 8, 1, 9)
        console.log(max); // 9
        ```

#### 3. `Function.prototype.bind()`

  * **作用**：**不立即执行**函数，而是创建一个**新函数**，这个新函数的 `this` 值被永久绑定到指定的 `thisArg`。

  * **语法**：`const newFunction = func.bind(thisArg, arg1, arg2, ...)`

  * **参数**：

      * `thisArg`：要绑定到新函数的 `this` 值。
      * `arg1, arg2, ...`：（可选）预设的参数，这些参数会优先于新函数调用时传入的参数。这个过程也称为“柯里化” (Currying)。

  * **返回值**：一个绑定了 `this` 和部分参数的新函数。

  * **代码示例**：

    ```javascript
    const module = {
      x: 42,
      getX: function() {
        return this.x;
      }
    };

    const unboundGetX = module.getX;
    console.log(unboundGetX()); // 输出: undefined (因为此时 this 指向全局 window)

    // 使用 bind 创建一个新函数，并将 this 永久绑定到 module
    const boundGetX = unboundGetX.bind(module);
    console.log(boundGetX()); // 输出: 42
    ```

      * **典型场景**: 在回调函数中使用，以确保 `this` 指向正确。
        ```javascript
        class App {
          constructor() {
            this.name = "My App";
          }
          start() {
            // 如果不使用 .bind(this)，setTimeout 中的 this 将指向 window
            setTimeout(this.logName.bind(this), 1000);
          }
          logName() {
            console.log(this.name); // 输出: My App
          }
        }
        const app = new App();
        app.start();
        ```

#### 核心区别总结

| 特性         | `call()`       | `apply()`      | `bind()`               |
| :----------- | :------------- | :------------- | :--------------------- |
| **执行时机** | 立即执行       | 立即执行       | 不执行，返回新函数     |
| **参数传递** | 逐个参数传递   | 传递参数数组   | 逐个参数传递（可预设） |
| **返回值**   | 原函数的返回值 | 原函数的返回值 | 绑定后的新函数         |


### new

在 JavaScript 中，new 操作符用于创建对象实例，其底层机制涉及原型链、this 绑定和构造函数执行。以下是其核心步骤及原理详解：

#### ⚙️ 1. 创建空对象

new 首先创建一个全新的空对象（{}），作为后续操作的载体。

#### 🔗 2. 设置原型链（关键步骤）

将新对象的原型（[[Prototype]]，即 __proto__）指向构造函数的 prototype 属性，使新对象能访问构造函数原型上的属性和方法。  
obj.__proto__ = Constructor.prototype;
// 或更规范的写法：Object.setPrototypeOf(obj, Constructor.prototype);


#### 🔧 3. 绑定 this 并执行构造函数

将构造函数内部的 this 指向新对象，并传入参数执行构造函数。此时，构造函数中的代码（如 this.name = name）会为新对象添加属性和方法。  
const result = Constructor.apply(obj, args);


#### 🔄 4. 处理返回值

• 若构造函数返回对象类型（如 {}、数组、函数），则返回该对象，忽略新创建的对象。  

• 若返回原始类型（如 number、string）或无返回值，则返回新创建的对象。  
return (typeof result === 'object' && result !== null) ? result : obj;


#### 💻 手动实现 new（代码模拟）

以下函数 myNew 模拟了 new 的行为：  
```javascript
function myNew(Constructor, ...args) {
    // 创建对象并链接原型
    const obj = Object.create(Constructor.prototype);
    // 执行构造函数并绑定 this
    const result = Constructor.apply(obj, args);
    // 根据返回值类型决定返回结果
    return (result !== null && typeof result === 'object') ? result : obj;
}
```


#### ⚠️ 关键注意事项

1. 忘记 new 的后果  
   直接调用构造函数（如 Person()）时，this 指向全局对象（浏览器中为 window），导致属性泄露到全局，或严格模式下报错。  

2. 构造函数返回值的影响  
   返回对象会覆盖默认实例，破坏原型链（如 instanceof 失效）；返回原始值则不影响。  
   function Foo() { return { name: "Bar" }; }
   const obj = new Foo(); // obj 为 { name: "Bar" }，而非 Foo 实例
   

3. ES6 类语法糖  
   class 本质仍是构造函数 + new，但强制必须通过 new 调用，否则报错。

#### 💎 总结

new 的本质是：创建对象 → 链接原型 → 初始化属性 → 按需返回。理解这一过程对掌握 JavaScript 面向对象编程、原型继承及框架源码分析至关重要。现代开发中推荐使用 class 语法，但底层仍依赖 new 的机制。