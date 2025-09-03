+++
date = '2025-07-08T13:31:44+08:00'
draft = false
title = 'js基础'
categories = ["逆向"]
tags = ["js", "基础"]
+++

## JavaScript 内存管理：栈内存与堆内存

### 核心概念

在 JavaScript 中，内存分配遵循一个基本的原则，这决定了不同类型的数据是如何存储和访问的：

  - **基本数据类型** (Primitive Types) 的变量，其**值**直接存储在 **栈内存 (Stack)** 中。
  - **引用数据类型** (Reference Types)，如对象 (Object)，其**内容**存储在 **堆内存 (Heap)** 中，而在 **栈内存 (Stack)** 中仅存储一个指向该堆内存地址的**引用**。

### 代码示例

以下代码展示了不同类型变量的声明：

```javascript
var a = 123;
var b = true;
var c = "hello";
var d = {name: 'sunwukong', age: 18};
```

  - `a`, `b`, `c` 是基本数据类型 (`Number`, `Boolean`, `String`)。
  - `d` 是引用数据类型 (`Object`)。

### 内存分配图解

根据上述代码，变量在内存中的存储状态如下所示：

#### 栈内存 (Stack)

栈内存存储了基本类型的值和引用类型的地址。它的特点是后进先出（LIFO），存取速度快。

| 变量名 | 值 (或地址) |
| :----- | :---------- |
| **d**  | `0x000`     |
| **c**  | `"hello"`   |
| **b**  | `true`      |
| **a**  | `123`       |

**说明:**

  - 变量 `a`, `b`, `c` 的值被直接存储在栈中。
  - 变量 `d` 的值是一个内存地址（例如 `0x000`），这个地址指向堆内存中存储的实际对象。

#### 堆内存 (Heap)

堆内存用于存储大小不固定、动态分配的数据，主要是对象。

| 内存地址 | 存储内容                         |
| :------- | :------------------------------- |
| `0x000`  | `{ name: 'sunwukong', age: 18 }` |

**说明:**

  - `{name: 'sunwukong', age: 18}` 这个对象实体被存储在堆内存中，其起始地址为 `0x000`。
  - 栈中的变量 `d` 通过存储这个地址 `0x000`，与堆中的对象建立了引用关系。当我们需要访问 `d` 的属性（如 `d.name`）时，JavaScript 引擎会先从栈中找到 `d` 的地址，然后根据地址去堆中找到对应的对象并读取其属性。

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

### 构造函数 (Constructor Function)

构造函数本质上是一个普通的函数，但它的设计和用途是专门为了**创建和初始化特定类型的对象**。它就像一个“对象模板”或“蓝图”，我们可以用它来批量生产结构和功能相似的对象。

在 JavaScript 中，按照惯例，构造函数的**首字母通常大写**，以便与普通函数区分开来。

#### 核心特点

构造函数有几个关键的特点，这都与 `new` 操作符紧密相关：

1.  **使用 `new` 关键字调用**：构造函数必须通过 `new` 关键字来调用，这样才能正确地创建新对象。
2.  **`this` 关键字**：在构造函数内部，`this` 关键字指向即将被创建的新对象。我们可以通过 `this` 来为新对象添加属性和方法。
3.  **隐式返回**：构造函数通常不需要显式地使用 `return` 语句。当使用 `new` 调用时，它会自动创建并返回新对象（即 `this`）。
      * **特例**：如果手动 `return` 一个对象，那么将返回这个对象而不是 `this`。如果手动 `return` 一个原始类型（如字符串、数字），则会被忽略，仍然返回 `this`。

#### `new` 关键字的工作流程

当我们使用 `new` 关键字调用一个构造函数时，JavaScript 引擎在背后执行了以下四个步骤：

1.  **创建新对象**：在内存中创建一个全新的空对象。
2.  **链接到原型**：将这个新对象的原型（`[[Prototype]]` 或 `__proto__`）指向构造函数的 `prototype` 属性。这使得新对象可以继承构造函数原型上的属性和方法。
3.  **绑定 `this`**：将构造函数内部的 `this` 指向这个新创建的对象。
4.  **返回新对象**：执行构造函数内部的代码（为新对象添加属性和方法），然后自动返回这个新对象（除非函数内部显式返回了另一个对象）。

#### 构造函数的使用示例

假设我们要创建多个描述“汽车”的对象，每个汽车都有品牌（brand）、型号（model）和颜色（color），并且都有一个 `start` 方法。

```javascript
// 1. 定义一个构造函数
function Car(brand, model, color) {
  // `this` 指向即将创建的新实例
  
  // 添加属性
  this.brand = brand;
  this.model = model;
  this.color = color;
  this.isRunning = false;

  // 添加方法
  this.start = function() {
    this.isRunning = true;
    console.log(this.brand + " " + this.model + " 启动了！");
  };
}

// 2. 使用 new 关键字创建实例（对象）
let car1 = new Car('Toyota', 'Camry', '白色');
let car2 = new Car('Honda', 'Civic', '黑色');
let car3 = new Car('Ford', 'Mustang', '红色');

// 3. 调用实例的属性和方法
console.log(car1.brand);    // 输出: Toyota
console.log(car2.color);    // 输出: 黑色

car3.start();               // 输出: Ford Mustang 启动了！
console.log(car3.isRunning); // 输出: true

console.log(car1 instanceof Car); // 输出: true, 验证 car1 是由 Car 构造的
```

在这个例子中，`Car` 就是一个构造函数，而 `car1`, `car2`, `car3` 则是通过 `Car` 这个“模板”创建出来的三个独立的对象实例。

#### 构造函数的使用场景

构造函数主要用于以下场景：

1.  **批量创建结构相同的对象**
    这是最核心的用途。当你的应用程序需要创建大量具有相同属性和方法的对象时（例如，游戏中的敌人、电商网站的商品、用户账户等），使用构造函数可以极大地简化代码，提高复用性。

2.  **实现古典的面向对象编程（OOP）范式**
    在 ES6 的 `class` 语法出现之前，构造函数是 JavaScript 实现类（Class）、继承（Inheritance）等面向对象概念的主要方式。通过原型链（prototype chain），可以实现属性和方法的继承。

    ```javascript
    // 在原型上添加方法，可以被所有实例共享，更节省内存
    Car.prototype.stop = function() {
      this.isRunning = false;
      console.log(this.brand + " 熄火了。");
    }

    car1.stop(); // car1 实例可以访问到原型上的方法
    ```

3.  **封装复杂对象的创建过程**
    如果一个对象的创建过程很复杂，需要进行多步初始化、数据处理或逻辑判断，可以将这些复杂的逻辑封装在构造函数内部。调用者只需通过 `new` 关键字传入必要的参数，就能得到一个初始化完毕的可用对象，无需关心内部的复杂细节。

4.  **构建 JavaScript 内置对象和库**
    JavaScript 的许多内置对象，如 `Object`, `Array`, `Date`, `RegExp` 等，本身就是构造函数。

    ```javascript
    let arr = new Array(1, 2, 3); // 使用 Array 构造函数
    let today = new Date();     // 使用 Date 构造函数
    ```

    许多第三方库（如早期的 jQuery 的 `$`）也广泛使用构造函数模式来创建其实例对象。

#### 总结

| 特性         | 描述                                                                                   |
| :----------- | :------------------------------------------------------------------------------------- |
| **定义**     | 一个用于创建和初始化对象的特殊函数，通常首字母大写。                                   |
| **调用方式** | 必须使用 `new` 关键字。                                                                |
| **核心机制** | `new` 操作符会自动创建新对象，并将函数内的 `this` 绑定到该对象上，最后隐式返回该对象。 |
| **主要用途** | 作为对象的“模板”，用于批量创建结构和功能相似的对象实例。                               |
| **历史地位** | 在 ES6 `class` 语法出现之前，是 JavaScript 实现面向对象编程的主要工具。                |

尽管现在 ES6 的 `class` 语法在底层仍然是基于构造函数和原型链的“语法糖”，并且更易于理解和使用，但理解构造函数的工作原理对于深入掌握 JavaScript 的对象、原型和继承机制仍然至关重要。


### class
自 ES6 (ECMAScript 2015) 发布以来，JavaScript 引入了 `class` 关键字，提供了一种更清晰、更简洁、更接近传统面向对象编程语言（如 Java 或 C++）的语法来创建对象和处理继承。

这种 `class` 语法本质上是 JavaScript 现有原型继承机制的“语法糖”（Syntactic Sugar），它并没有引入新的对象继承模型，但让代码的组织结构和可读性大大增强。

下面我们来详细了解现代 JavaScript `class` 的写法。

#### 1. 基本的 Class 定义和实例化

一个基本的类包含 `class` 关键字、类名（通常首字母大写）和一对花括号 `{}`。

  - **`constructor` 方法**：这是一个特殊的方法，用于创建和初始化一个由 `class` 创建的对象。一个类只能有一个 `constructor` 方法。当使用 `new` 关键字创建类的实例时，`constructor` 会被自动调用。
  - **实例化**：和构造函数一样，使用 `new` 关键字来创建类的实例。

<!-- end list -->

```javascript
// 1. 定义一个类
class Car {
  // 构造函数，在 new 一个实例时自动执行
  constructor(brand, model, color) {
    // this 指向实例对象
    this.brand = brand;
    this.model = model;
    this.color = color;
    this.isRunning = false; // 默认属性
  }

  // 定义方法（方法会被添加到 Car.prototype 上）
  start() {
    this.isRunning = true;
    console.log(`${this.brand} ${this.model} 启动了！`);
  }

  stop() {
    this.isRunning = false;
    console.log(`${this.brand} ${this.model} 熄火了。`);
  }
}

// 2. 使用 new 关键字创建实例
let myCar = new Car('Tesla', 'Model 3', '白色');

// 3. 调用属性和方法
console.log(myCar.brand); // 输出: Tesla
myCar.start();            // 输出: Tesla Model 3 启动了！
console.log(myCar.isRunning); // 输出: true
myCar.stop();             // 输出: Tesla Model 3 熄火了。

console.log(typeof Car);  // 输出: "function" (证明 class 本质上还是函数)
```

#### 2. 继承 (Inheritance)

`class` 语法让实现继承变得非常简单，主要使用 `extends` 和 `super` 两个关键字。

  - **`extends`**：用于创建一个类的子类。
  - **`super`**：用于调用父类的构造函数或方法。
      - 在子类的 `constructor` 中，必须先调用 `super()` 才能使用 `this` 关键字。`super()` 会执行父类的构造函数。
      - 在子类的方法中，可以通过 `super.methodName()` 来调用父类的同名方法。

<!-- end list -->

```javascript
// 父类 (基类)
class Vehicle {
  constructor(name) {
    this.name = name;
  }

  move() {
    console.log(`${this.name} 正在移动。`);
  }
}

// 子类 ElectricCar 继承自 Vehicle
class ElectricCar extends Vehicle {
  constructor(name, batteryCapacity) {
    // 1. 必须先调用父类的构造函数
    super(name); // 调用 Vehicle 的 constructor(name)

    // 2. 然后再添加子类自己的属性
    this.batteryCapacity = batteryCapacity;
  }

  // 子类可以有自己的方法
  charge() {
    console.log(`${this.name} 正在充电，电池容量为 ${this.batteryCapacity} kWh。`);
  }

  // 子类可以重写 (override) 父类的方法
  move() {
    // 通过 super 可以调用父类的同名方法
    super.move(); 
    console.log(`${this.name} 是电动的，行驶时非常安静。`);
  }
}

let myEV = new ElectricCar('NIO ET7', 100);

myEV.move();
// 输出:
// NIO ET7 正在移动。
// NIO ET7 是电动的，行驶时非常安静。

myEV.charge();
// 输出:
// NIO ET7 正在充电，电池容量为 100 kWh。
```

#### 3. Getters 和 Setters

`class` 内部可以像对象字面量一样，使用 `get` 和 `set` 关键字来定义存取器属性。这允许你为属性的读取和设置添加自定义逻辑。

```javascript
class User {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // Getter: 获取 fullName 属性时调用
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // Setter: 给 fullName 属性赋值时调用
  set fullName(name) {
    const [firstName, lastName] = name.split(' ');
    this.firstName = firstName;
    this.lastName = lastName;
  }
}

let user = new User('John', 'Doe');

// 调用 getter
console.log(user.fullName); // 输出: John Doe

// 调用 setter
user.fullName = 'Jane Smith';

console.log(user.firstName); // 输出: Jane
console.log(user.lastName);  // 输出: Smith
```

#### 4. 静态方法和静态属性 (Static Members)

静态成员是直接附加在类本身，而不是类的实例上的属性或方法。调用它们时需要使用类名，而不是实例名。它们通常用于创建工具函数或定义类级别的常量。

  - **`static` 关键字**：用于定义静态方法或静态属性。

<!-- end list -->

```javascript
class MathHelper {
  static PI = 3.14159; // 静态属性

  // 静态方法
  static square(x) {
    return x * x;
  }
  
  static cube(x) {
    return x * x * x;
  }
}

// 直接通过类名调用，无需创建实例
console.log(MathHelper.PI);         // 输出: 3.14159
console.log(MathHelper.square(5)); // 输出: 25
console.log(MathHelper.cube(3));   // 输出: 27

// let helper = new MathHelper();
// console.log(helper.square(5)); // 错误！静态方法不能通过实例调用
```

#### 总结：`class` 与传统构造函数的对比

| 特性             | `class` 语法                                      | 传统构造函数 (ES5)                             |
| :--------------- | :------------------------------------------------ | :--------------------------------------------- |
| **定义方式**     | 使用 `class` 关键字，结构清晰                     | 定义一个普通函数                               |
| **构造器**       | `constructor` 方法                                | 函数本身                                       |
| **方法定义**     | 直接在 `class` 块内定义，不使用 `function` 关键字 | 在构造函数的 `prototype` 对象上添加            |
| **继承**         | 使用 `extends` 和 `super`，直观易懂               | 复杂的原型链操作和 `call`/`apply` 借用构造函数 |
| **调用**         | **必须**使用 `new` 关键字调用                     | 可以作为普通函数调用（容易出错）               |
| **代码提升**     | 不存在 (Hoisting)，必须先定义再使用               | 存在函数提升                                   |
| **"use strict"** | 类和模块的内部默认就是严格模式                    | 需要手动开启                                   |

总而言之，`class` 提供了一种更现代化、更优雅的方式来组织和编写基于对象的 JavaScript 代码，尤其是在构建大型应用程序时，它的结构化优势非常明显。


### 原型链

一个原型链的例子：
[![image.png](https://i.postimg.cc/k4FK4p7x/image.png)](https://postimg.cc/bDrsVLDJ)

注解：
1.  **`prototype`**：是**函数**（或 Class）才有的属性。它指向一个对象，这个对象是该函数作为构造函数时，创建出来的所有**实例**的“共享仓库”或“原型”。
2.  **`__proto__`**：是每个**对象**（包括实例和函数）都有的内部链接，它指向创建这个对象的构造函数的 `prototype`。`__proto__` 是构成原型链的实际链接。
3.  **黄金法则**：`实例.__proto__ === 构造函数.prototype`。这是整张图的核心关系。
4.  **万物皆对象**：在 JavaScript 中，函数也是一种特殊的对象。
5.  `Function.__proto__`->`Function.prototype`:   `Function`本身也是一个函数，所以它也是由自己创建的。因此，`Function` 构造函数这个对象的 `__proto__` 指向它自己的 `prototype`，即 `Function.prototype`。

关于原型链：
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