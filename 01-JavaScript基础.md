# JavaScript基础

## 【必考点 - 超高频】⭐⭐⭐

### 1. JavaScript有哪些数据类型，它们的区别？

**回答：**

JavaScript 数据类型分为两类：**原始类型（基本类型）** 和 **引用类型**。

### 原始类型（7种）

| 类型 | 说明 | 示例 |
|------|------|------|
| `Number` | 数值类型，包含整数和浮点数 | `1`, `3.14`, `NaN` |
| `String` | 字符串类型 | `'hello'`, `"world"` |
| `Boolean` | 布尔类型 | `true`, `false` |
| `Undefined` | 未定义，表示变量未赋值 | `undefined` |
| `Null` | 空值，表示刻意设置为空 | `null` |
| `Symbol` | 唯一标识符（ES6+） | `Symbol('id')` |
| `BigInt` | 大整数（ES11+） | `9007199254740991n` |

### 引用类型（1种）

| 类型 | 说明 |
|------|------|
| `Object` | 对象类型，包括数组、函数、日期等 |

### 主要区别

1. **存储方式不同**
   - 原始类型：存储在**栈内存**中，按值访问
   - 引用类型：存储在**堆内存**中，变量保存的是**内存地址（引用）**

2. **赋值方式不同**
   ```javascript
   // 原始类型 - 值的拷贝
   let a = 1;
   let b = a;  // b 是 1 的拷贝
   b = 2;      // 修改 b 不影响 a
   console.log(a); // 1

   // 引用类型 - 引用的拷贝
   let obj1 = { name: 'Tom' };
   let obj2 = obj1;  // obj2 保存的是同一个内存地址
   obj2.name = 'Jack'; // 修改 obj2 会影响 obj1
   console.log(obj1.name); // 'Jack'
   ```

3. **比较方式不同**
   ```javascript
   // 原始类型 - 比较值
   1 === 1  // true

   // 引用类型 - 比较内存地址
   {} === {} // false，两个空对象是不同的引用
   ```

4. **函数参数传递不同**
   - 原始类型：按值传递（拷贝值）
   - 引用类型：按引用传递（拷贝地址）

---

### 2. 数据类型检测的方式有哪些？

**回答：**

### 1. typeof 运算符

```javascript
typeof 1           // 'number'
typeof 'hello'     // 'string'
typeof true       // 'boolean'
typeof undefined   // 'undefined'
typeof Symbol('id') // 'symbol'
typeof 123n       // 'bigint'
typeof {}          // 'object'
typeof []          // 'object'
typeof null        // 'object'  ★ 历史bug
typeof function(){} // 'function'
```

**优点**：简单快速
**缺点**：`null` 会返回 `'object'`（历史bug），无法细分对象类型

---

### 2. instanceof 运算符

```javascript
[] instanceof Array        // true
{} instanceof Object       // true
new Date() instanceof Date // true
(function(){}) instanceof Function // true
```

**优点**：可以检测具体对象类型（Array、Date、RegExp等）
**缺点**：不能检测原始类型，只适用于对象

---

### 3. Object.prototype.toString.call()

```javascript
Object.prototype.toString.call(1)           // '[object Number]'
Object.prototype.toString.call('hello')     // '[object String]'
Object.prototype.toString.call(true)        // '[object Boolean]'
Object.prototype.toString.call(undefined)   // '[object Undefined]'
Object.prototype.toString.call(null)        // '[object Null]'
Object.prototype.toString.call({})          // '[object Object]'
Object.prototype.toString.call([])          // '[object Array]'
Object.prototype.toString.call(new Date())  // '[object Date]'
```

**优点**：最准确，能检测所有类型，包括原始类型和具体对象类型
**缺点**：写法繁琐


### 总结

| 方式 | 适用场景 |
|------|----------|
| `typeof` | 快速检测原始类型（除null外） |
| `instanceof` | 检测对象类型（需知道构造函数） |
| `Object.prototype.toString.call()` | 最全面准确的检测 |
| `Array.isArray()` | 专门检测数组 |

---

### 3. null和undefined的区别

**回答：**

| 区别 | `null` | `undefined` |
|------|--------|-------------|
| **含义** | 刻意设置为"空值" | 未定义，未赋值 |
| **类型** | `object`（历史bug） | `undefined` |
| **场景** | 变量初始化时主动赋值 | 变量声明但未赋值 |
| **转换为数字** | `Number(null)` → `0` | `Number(undefined)` → `NaN` |
| **`==` 比较** | `null == undefined` → `true` | 同左 |
| **`===` 比较** | `null === undefined` → `false` | 同左 |

### 实际使用建议

```javascript
// 使用 null - 明确表示"这里应该有个值，但暂时没有"
let user = null;  // 稍后会赋值

// 使用 undefined - 自然产生，表示"没有定义"
let arr = [1, 2, 3];
console.log(arr[5]); // undefined，数组中不存在这个索引

// 判断
typeof null        // 'object' （bug）
typeof undefined   // 'undefined'
```

---

### 4. typeof null 的结果是什么，为什么？

**回答：**

`typeof null` 的结果是 `'object'`

**原因：**

这是 JavaScript 的一个**历史bug**，起源于 JavaScript 最初的实现。

在 JavaScript 最初版本中，数据以 32 位字长存储，其中有一个标志位（tag）来区分数据类型：
- 000: object
- 1: int
- 010: double
- 100: string
- 110: boolean

当时 `null` 被设计为表示空对象指针（全0），所以它的标志位是 000，被误判为 object 类型。

这个bug一直延续至今，已无法修复（因为会破坏大量现有代码）。

```javascript
console.log(typeof null); // 'object'
```

---

### 5. 为什么0.1+0.2 !== 0.3，如何让其相等？

**回答：**

**原因：**

JavaScript 使用 **IEEE 754** 标准的**浮点数表示法**，存在精度丢失问题。

```javascript
0.1 + 0.2  // 0.30000000000000004
0.1 + 0.2 === 0.3  // false
```

**原理：**

- 0.1 和 0.2 转换为二进制是无限循环小数
- JavaScript 使用 64 位浮点数（双精度），只能保留 52 位尾数
- 计算时会产生精度误差

**解决方法：**

1. **使用 Number.EPSILON**
   ```javascript
   function isEqual(a, b) {
     return Math.abs(a - b) < Number.EPSILON;
   }
   isEqual(0.1 + 0.2, 0.3); // true
   ```

2. **转换为整数计算**
   ```javascript
   (0.1 * 10 + 0.2 * 10) / 10 === 0.3  // true
   ```

3. **使用 toFixed**
   ```javascript
   (0.1 + 0.2).toFixed(2) === '0.30'  // true
   ```

4. **使用第三方库**（如 decimal.js）

---

### 6. typeof NaN 的结果是什么？

**回答：**

`typeof NaN` 的结果是 `'number'`

```javascript
typeof NaN  // 'number'
```

这是因为 NaN 本质上是一个"数字类型的无效值"，所以 typeof 返回 'number'。

**注意：** NaN 是唯一一个与自身不相等的值：
```javascript
NaN === NaN  // false
isNaN(NaN)   // true
Number.isNaN(NaN) // true
```

---

### 7. isNaN 和 Number.isNaN 函数的区别？

**回答：**

| 特点 | `isNaN()` | `Number.isNaN()` |
|------|-----------|------------------|
| **参数为NaN** | `isNaN(NaN)` → `true` | `Number.isNaN(NaN)` → `true` |
| **参数为数字** | `isNaN(123)` → `false` | `Number.isNaN(123)` → `false` |
| **参数为字符串** | `isNaN('hello')` → `true` | `Number.isNaN('hello')` → `false` |
| **参数为undefined** | `isNaN(undefined)` → `true` | `Number.isNaN(undefined)` → `false` |
| **参数为null** | `isNaN(null)` → `false` | `Number.isNaN(null)` → `false` |

**区别：**

- `isNaN()` 会先**尝试将参数转换为数字**再判断
- `Number.isNaN()` 更严格，只有参数**严格等于 NaN** 才返回 true

```javascript
// isNaN 会进行类型转换
isNaN('hello')     // true  ('hello' 转数字变成 NaN)
isNaN(undefined)  // true  (undefined 转数字变成 NaN)
isNaN('123')      // false ('123' 转数字变成 123)

// Number.isNaN 不会进行类型转换
Number.isNaN('hello')     // false
Number.isNaN(undefined)  // false
Number.isNaN('123')      // false
```

**推荐使用 `Number.isNaN()`**，更准确可靠。

---

### 8. let、const、var的区别

**回答：**

| 特性 | var | let | const |
|------|-----|-----|-------|
| 作用域 | 函数作用域 | 块级作用域 | 块级作用域 |
| 变量提升 | 是（值为undefined） | 是（暂时性死区） | 是（暂时性死区） |
| 重复声明 | 可以 | 不可以 | 不可以 |
| 全局挂载window | 是 | 否 | 否 |

### var - 函数作用域

```javascript
function test() {
  if (true) {
    var a = 1;
  }
  console.log(a); // 1，var 是函数作用域
}
```

### let - 块级作用域

```javascript
if (true) {
  let a = 1;
}
console.log(a); // ReferenceError，let 是块级作用域
```

### const - 块级作用域 + 不可修改

```javascript
const a = 1;
a = 2; // TypeError: 赋值给常量

// 但对象属性可以修改
const obj = { name: 'Tom' };
obj.name = 'Jack'; // 可以
obj = {}; // TypeError: 不能重新赋值
```

### 变量提升与暂时性死区

```javascript
console.log(a); // undefined（变量提升）
var a = 1;

console.log(b); // ReferenceError（暂时性死区）
let b = 1;
```

### 最佳实践：默认使用 const，需要重新赋值时用 let，避免用 var

---

### 9. const对象的属性可以修改吗？

**回答：**

**可以修改属性，但不能重新赋值。**

```javascript
const obj = { name: 'Tom' };
obj.name = 'Jack';  // ✓ 可以修改属性
obj.age = 20;       // ✓ 可以添加属性
delete obj.name;    // ✓ 可以删除属性
obj = {};           // ✗ TypeError: 不能重新赋值
```

### 原理

- `const` 保证的是**变量绑定的引用不可改变**，而不是**对象内容不可改变**
- 对象本身存储在堆内存中，变量只是存储了指向堆内存的地址
- const 让变量只能指向同一个对象，但对象内部可以随意修改

### 如果需要深冻结

```javascript
const obj = { name: 'Tom', nested: { age: 20 } };

Object.freeze(obj); // 浅冻结
obj.name = 'Jack';   // 严格模式下会报错，普通模式无效

// 深冻结需要递归
function deepFreeze(obj) {
  Object.freeze(obj);
  for (let key in obj) {
    if (typeof obj[key] === 'object') {
      deepFreeze(obj[key]);
    }
  }
}
```

---

### 10. 箭头函数与普通函数的区别

**回答：**

| 区别 | 箭头函数 | 普通函数 |
|------|----------|----------|
| this绑定 | 指向定义时的外层作用域 | 指向调用时的对象 |
| arguments | 没有自己的arguments | 有自己的arguments |
| constructor | 不能作为构造函数 | 可以作为构造函数 |
| 原型 | 没有prototype | 有prototype |
| yield | 不能作为Generator函数 | 可以作为Generator函数 |
| 语法 | 简洁 | 完整 |

### 代码示例

```javascript
// 1. this 绑定不同
const obj = {
  name: 'Tom',
  regularFn: function() {
    console.log(this.name); // 'Tom'
  },
  arrowFn: () => {
    console.log(this.name); // undefined（指向window）
  }
};

const regularFn = obj.regularFn;
regularFn(); // 'Tom'（谁调用指向谁）

const arrowFn = obj.arrowFn;
arrowFn(); // undefined（定义时确定）

// 2. constructor
const Fn = () => {};
new Fn(); // TypeError: Fn 不是构造函数

function RegularFn() {}
new RegularFn(); // ✓

// 3. arguments
function regular() {
  console.log(arguments); // [1, 2, 3]
}
regular(1, 2, 3);

const arrow = () => {
  console.log(arguments); // ReferenceError
};
arrow(1, 2, 3);

// 4. 简洁语法
const add = (a, b) => a + b;
const fn = () => ({ name: 'Tom' });
```

---

### 11. 箭头函数的this指向哪里？

**回答：**

箭头函数的 this 指向**定义时所在的外层作用域的 this**（静态绑定）。

### 核心规则

箭头函数没有自己的 this，它会捕获定义时最近外层函数的 this。

```javascript
const obj = {
  name: 'Tom',
  fn: function() {
    // 这里的 this 指向 obj
    const arrow = () => {
      console.log(this.name); // 'Tom'
    };
    arrow();
  }
};

obj.fn();
```

### 对比：普通函数的 this 指向调用者

```javascript
const obj = {
  name: 'Tom',
  fn: function() {
    console.log(this.name); // 'Tom'
  }
};

const fn = obj.fn;
fn(); // undefined（普通函数 this 指向 window）
```

### 实际场景

```javascript
// 定时器中的 this 问题
class Counter {
  constructor() {
    this.count = 0;
  }

  // 普通函数：需要 bind 或 arrow
  increment() {
    setInterval(function() {
      console.log(this.count); // undefined（指向window）
    }, 1000);
  }

  // 箭头函数：自动捕获外层 this
  incrementArrow() {
    setInterval(() => {
      console.log(this.count); // 0
    }, 1000);
  }
}
```

### 总结

- 箭头函数：this 是**静态**的，由定义时外层作用域决定
- 普通函数：this 是**动态**的，由调用时决定（谁调用指向谁）

---

### 12. 扩展运算符的作用及使用场景

**回答：**

扩展运算符 `...` 可以将**可迭代对象**展开为单个元素。

### 使用场景

```javascript
// 1. 数组展开
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

// 2. 对象展开（ES9+）
const obj1 = { name: 'Tom', age: 20 };
const obj2 = { ...obj1, city: 'Beijing' }; // { name: 'Tom', age: 20, city: 'Beijing' }

// 3. 函数参数
const nums = [1, 2, 3];
Math.max(...nums); // 3 等同于 Math.max(1, 2, 3)

// 4. 复制数组（浅拷贝）
const original = [1, 2, 3];
const copy = [...original];

// 5. 合并数组
const a = [1, 2];
const b = [3, 4];
const combined = [...a, ...b]; // [1, 2, 3, 4]

// 6. 复制对象
const obj = { name: 'Tom' };
const copyObj = { ...obj };

// 7. 类数组对象转换为数组
const likeArray = { 0: 'a', 1: 'b', length: 2 };
const arr = [...likeArray]; // ['a', 'b']
```

---

### 13. 对对象与数组的解构的理解

**回答：**

解构赋值是一种从数组或对象中提取值的语法。

### 数组解构

```javascript
// 基本用法
const [a, b, c] = [1, 2, 3];
// a = 1, b = 2, c = 3

// 跳过元素
const [a, , c] = [1, 2, 3];
// a = 1, c = 3

// 剩余模式
const [a, ...rest] = [1, 2, 3, 4];
// a = 1, rest = [2, 3, 4]

// 默认值
const [a = 1, b = 2] = [undefined, 3];
// a = 1, b = 3

// 交换变量
let a = 1, b = 2;
[a, b] = [b, a];
// a = 2, b = 1
```

### 对象解构

```javascript
// 基本用法
const { name, age } = { name: 'Tom', age: 20 };
// name = 'Tom', age = 20

// 重命名
const { name: n, age: a } = { name: 'Tom', age: 20 };
// n = 'Tom', a = 20

// 默认值
const { name = 'Tom', age = 0 } = { age: 20 };
// name = 'Tom', age = 20

// 剩余模式
const { name, ...rest } = { name: 'Tom', age: 20, city: 'Beijing' };
// name = 'Tom', rest = { age: 20, city: 'Beijing' }

// 嵌套解构
const obj = {
  user: {
    name: 'Tom',
    address: { city: 'Beijing' }
  }
};
const { user: { name, address: { city } } } = obj;
// name = 'Tom', city = 'Beijing'
```

### 函数参数解构

```javascript
// 对象参数解构
function fn({ name, age = 20 }) {
  console.log(name, age);
}
fn({ name: 'Tom' }); // 'Tom', 20

// 数组参数解构
function fn([a, b]) {
  console.log(a, b);
}
fn([1, 2]); // 1, 2
```

---

### 14. 对 rest 参数的理解

**回答：**

`...rest` 剩余参数将多余的参数收集为一个数组。

```javascript
// 收集多余参数
function fn(a, b, ...rest) {
  console.log(a, b, rest);
}
fn(1, 2, 3, 4, 5);
// a = 1, b = 2, rest = [3, 4, 5]

// 与解构结合
const [first, ...others] = [1, 2, 3, 4];
// first = 1, others = [2, 3, 4]

// 替代 arguments
function fn(...args) {
  console.log(args); // 是真正的数组
}
fn(1, 2, 3);

// 注意事项：rest 必须是最后一个参数
function fn(a, ...rest, b) {} // SyntaxError
```

---

### 15. 对原型的理解

**回答：**

每个 JavaScript 对象都有一个 `prototype`（原型对象），对象可以从原型继承属性和方法。

### 原型链

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function() {
  console.log('Hello, I am ' + this.name);
};

const tom = new Person('Tom');
tom.sayHello(); // 'Hello, I am Tom'

// tom 的原型链:
// tom -> Person.prototype -> Object.prototype -> null
```

### prototype vs __proto__

| 概念 | 说明 |
|------|------|
| `prototype` | 函数属性，指向原型对象 |
| `__proto__` | 对象属性，指向构造函数的 prototype |

```javascript
function Fn() {}
const fn = new Fn();

fn.__proto__ === Fn.prototype // true
```

### 原型的作用

1. **共享方法**：所有实例共享同一个方法，节省内存
2. **继承**：实现对象间的属性和方法继承

```javascript
// 原型链继承
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(this.name + ' is eating');
};

function Dog(name, breed) {
  Animal.call(this, name); // 借用构造函数
  this.breed = breed;
}

Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
```

---

### 16. 原型链的理解

**回答：**

原型链是 JavaScript 实现继承的核心机制，每个对象通过 `__proto__` 属性指向它的原型，形成一条链式结构。

### 原型链结构

```javascript
function Person(name) {
  this.name = name;
}

const tom = new Person('Tom');

console.log(tom.__proto__ === Person.prototype);        // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__);                // null
```

### 原型链查找

```javascript
tom.toString(); // 继承自 Object.prototype
tom.hasOwnProperty('name'); // true，自身属性
```

### 原型链的终点

```
tom -> Person.prototype -> Object.prototype -> null
```

---

### 17. 原型修改、重写

**回答：**

### 原型修改

```javascript
function Person() {}
Person.prototype.name = 'Tom';

const p1 = new Person();
console.log(p1.name); // 'Tom'

Person.prototype.name = 'Jack';
console.log(p1.name); // 'Jack'（已创建的实例也会受影响）
```

### 原型重写

```javascript
function Person(name) {
  this.name = name;
}

// 重写整个原型对象
Person.prototype = {
  constructor: Person, // 需要手动指定 constructor
  sayHello() {
    console.log('Hello');
  }
};

const p = new Person();
p.sayHello(); // 'Hello'
```

### 原型继承

```javascript
// 原型链继承
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log('eating');
};

function Dog(name) {
  Animal.call(this, name);
}

// 关键：创建 Animal.prototype 的副本
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// 添加 Dog 特有的方法
Dog.prototype.bark = function() {
  console.log('barking');
};
```

---

### 18. 对闭包的理解

**回答：**

闭包是指**函数内部访问外部作用域变量**的机制，即使外部函数已经执行完毕，内部函数仍然可以访问外部函数的变量。

### 闭包的形成

```javascript
function outer() {
  const a = 1;  // 外部函数的变量

  function inner() {
    console.log(a); // 内部函数访问外部变量
  }

  return inner;
}

const fn = outer(); // outer 执行完毕，但 a 仍被 inner 引用
fn(); // 1，闭包生效
```

### 闭包的作用

1. **私有变量**
   ```javascript
   function createCounter() {
     let count = 0;
     return {
       increment() { count++; },
       getCount() { return count; }
     };
   }

   const counter = createCounter();
   counter.increment();
   console.log(counter.getCount()); // 1
   // count 无法从外部直接访问，实现了私有变量
   ```

2. **函数工厂**
   ```javascript
   function multiply(factor) {
     return function(num) {
       return num * factor;
     };
   }

   const double = multiply(2);
   const triple = multiply(3);
   console.log(double(5)); // 10
   console.log(triple(5)); // 15
   ```

3. **延续变量生命周期**

### 闭包的问题（内存泄漏）

```javascript
// 内存泄漏：闭包引用大量数据
function heavy() {
  const largeData = new Array(1000000).fill('x');

  return function() {
    console.log(largeData[0]);
  };
}

const fn = heavy();
// largeData 不会被回收，因为被闭包引用
```

---

### 19. 对作用域、作用域链的理解

**待回答**

---

### 20. 对执行上下文的理解

**待回答**

---

## 【常考点 - 高频】⭐⭐

### 21. 判断数组的方式有哪些

**待回答**

---

### 22. instanceof 操作符的实现原理及实现

**待回答**

---

### 23. 如何获取安全的 undefined 值？

**待回答**

---

### 24. 其他值到字符串的转换规则？

**待回答**

---

### 25. 其他值到数字值的转换规则？

**待回答**

---

### 26. 其他值到布尔类型的值的转换规则？

**待回答**

---

### 27. || 和 && 操作符的返回值？

**待回答**

---

### 28. Object.is() 与比较操作符 "==="、"==" 的区别？

**待回答**

---

### 29. 什么是 JavaScript 中的包装类型？

**待回答**

---

### 30. JavaScript 中如何进行隐式类型转换？

**待回答**

---

### 31. 操作符什么时候用于字符串的拼接？

**待回答**

---

### 32. 为什么会有BigInt的提案？

**待回答**

---

### 33. object.assign和扩展运算法是深拷贝还是浅拷贝，两者区别

**待回答**

---

### 34. 如何判断一个对象是空对象

**待回答**

---

### 35. 如果new一个箭头函数的会怎么样

**待回答**

---

### 36. 如何提取高度嵌套的对象里的指定属性？

**待回答**

---

### 37. ES6中模板语法与字符串处理

**待回答**

---

### 38. map和Object的区别

**待回答**

---

### 39. map和weakMap的区别

**待回答**

---

### 40. JavaScript有哪些内置对象

**待回答**

---

## 【了解点 - 中低频】⭐

### 41. 常用的正则表达式有哪些？

**待回答**

---

### 42. 对JSON的理解

**待回答**

---

### 43. JavaScript脚本延迟加载的方式有哪些？

**待回答**

---

### 44. JavaScript 类数组对象的定义？

**待回答**

---

### 45. 数组有哪些原生方法？

**待回答**

---

### 46. Unicode、UTF-8、UTF-16、UTF-32的区别？

**待回答**

---

### 47. 常见的位运算符有哪些？其计算规则是什么？

**待回答**

---

### 48. 对类数组对象的理解，如何转化为数组

**待回答**

---

### 49. 对ES6模块与CommonJS模块有什么异同

**待回答**

---

### 50. 如何获得对象非原型链上的属性？

**待回答**

---
