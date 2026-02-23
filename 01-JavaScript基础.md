# JavaScript基础（55道）

## 1.1 作用域/闭包/执行上下文（12道）

### 1. 什么是作用域？JavaScript有哪些作用域类型？

**回答：**

作用域（Scope）是指变量和函数的可访问范围，决定了代码中变量和函数的可见性和生命周期。

### JavaScript 的作用域类型

| 作用域类型 | 说明 | 示例 |
|-----------|------|------|
| **全局作用域** | 代码中任何地方都能访问 | 在函数外声明的变量 |
| **函数作用域** | 在函数内部声明，函数外无法访问 | `function` 内部用 `var` 声明 |
| **块级作用域** | 在 `{}` 块内声明，块外无法访问 | `let`/`const` 声明的变量 |
| **模块作用域** | ES6 模块内部，模块外无法直接访问 | `import`/`export` 的模块 |

### 代码示例

```javascript
// 全局作用域
var globalVar = '我是全局变量';

function fn() {
  // 函数作用域
  var fnVar = '我是函数作用域变量';
  
  if (true) {
    // 块级作用域（let/const）
    let blockVar = '我是块级作用域变量';
    const blockConst = '我是块级常量';
    
    // var 没有块级作用域
    var noBlockVar = '我没有块级作用域';
  }
  
  console.log(blockVar);  // ReferenceError: 块外无法访问
  console.log(noBlockVar); // '我没有块级作用域' - 可以访问
}

console.log(fnVar); // ReferenceError: 函数外无法访问
```

### 作用域的作用

1. **隔离变量**：避免命名冲突
2. **控制访问**：实现封装和私有变量
3. **管理内存**：作用域结束后变量可被回收

---

### 2. 什么是作用域链？如何理解其作用？

**回答：**

作用域链（Scope Chain）是 JavaScript 查找变量的一种机制，当访问一个变量时，JavaScript 引擎会从当前作用域开始，逐级向上查找，直到找到该变量或到达全局作用域。

### 作用域链的形成

```javascript
const a = '全局a';

function outer() {
  const b = 'outer的b';
  
  function inner() {
    const c = 'inner的c';
    console.log(a, b, c); // 可以访问所有变量
  }
  
  inner();
}

outer();
```

### 作用域链结构

```
inner 作用域 → outer 作用域 → 全局作用域
     ↑              ↑              ↑
   [c]          [b]            [a]
```

### 查找过程

```javascript
const x = 1;

function foo() {
  const y = 2;
  
  function bar() {
    const z = 3;
    console.log(x + y + z); // 6
    // 查找顺序：bar作用域(z) → foo作用域(y) → 全局作用域(x)
  }
  
  bar();
}

foo();
```

### 作用域链的作用

1. **变量查找**：确定变量的值
2. **闭包形成**：内部函数可以访问外部变量
3. **内存管理**：作用域链帮助垃圾回收判断变量是否还在被引用

---

### 3. 什么是变量提升？let/const/var的提升行为有何不同？

**回答：**

变量提升（Hoisting）是指 JavaScript 在编译阶段将变量和函数声明移动到其作用域顶部的行为。

### var 的变量提升

```javascript
console.log(a); // undefined（不会报错，但值为 undefined）
var a = 1;
console.log(a); // 1

// 实际执行顺序相当于：
var a;          // 声明提升到顶部
console.log(a); // undefined
a = 1;          // 赋值留在原地
console.log(a); // 1
```

### let/const 的提升行为

```javascript
console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 2;

console.log(c); // ReferenceError: Cannot access 'c' before initialization
const c = 3;
```

### 对比总结

| 特性 | var | let | const |
|------|-----|-----|-------|
| **提升** | 是（声明提升） | 是（声明提升） | 是（声明提升） |
| **初始化** | 自动初始化为 undefined | 不自动初始化 | 不自动初始化 |
| **暂时性死区** | 无 | 有 | 有 |
| **重复声明** | 允许 | 不允许 | 不允许 |
| **作用域** | 函数作用域 | 块级作用域 | 块级作用域 |

### 暂时性死区（TDZ）

```javascript
// let/const 虽然提升了声明，但在声明之前访问会报错
// 这个"从作用域开始到声明之间"的区域就是暂时性死区

function fn() {
  console.log(x); // ReferenceError: TDZ
  let x = 1;
}
```

### 函数提升

```javascript
// 函数声明会整体提升
foo(); // 'hello'
function foo() {
  console.log('hello');
}

// 函数表达式不会提升
bar(); // TypeError: bar is not a function
var bar = function() {
  console.log('world');
};
```

---

### 4. 什么是暂时性死区（TDZ）？

**回答：**

暂时性死区（Temporal Dead Zone，简称 TDZ）是指在代码块内，使用 `let` 或 `const` 声明变量之前，该变量不可用的区域。

### TDZ 的产生

```javascript
{
  // TDZ 开始
  console.log(value); // ReferenceError: Cannot access 'value' before initialization
  // TDZ 结束
  let value = 10;
  console.log(value); // 10
}
```

### TDZ 的范围

```javascript
{
  // TDZ 开始于作用域顶部
  
  function fn() {
    console.log(x); // 这里也会报错，因为 fn 在 TDZ 内定义
  }
  
  // TDZ 结束
  let x = 1;
  
  fn(); // 如果这里调用，x 已经初始化，不会报错
}
```

### typeof 在 TDZ 中的表现

```javascript
// 正常情况
console.log(typeof undeclaredVar); // 'undefined'

// TDZ 中
console.log(typeof tdzVar); // ReferenceError
let tdzVar;
```

### TDZ 的作用

1. **防止变量在声明前被使用**：避免逻辑错误
2. **保证代码的可预测性**：明确变量的生命周期
3. **与 var 区分**：let/const 更严格，减少意外行为

### 对比示例

```javascript
// var - 没有 TDZ
console.log(a); // undefined
var a = 1;

// let - 有 TDZ
console.log(b); // ReferenceError
let b = 2;

// const - 有 TDZ
console.log(c); // ReferenceError
const c = 3;
```

### 最佳实践

```javascript
// ✅ 推荐：先声明，后使用
let count;
count = 0;
console.log(count);

// ❌ 避免：在 TDZ 中使用
console.log(count);
let count = 0; // 报错
```

---

### 5. 什么是执行上下文？执行上下文栈是什么？

**回答：**

执行上下文（Execution Context）是 JavaScript 代码执行时的环境，它定义了变量或函数有权访问的其他数据，以及它们的行为。

### 执行上下文的类型

| 类型 | 说明 | 触发场景 |
|------|------|----------|
| **全局执行上下文** | 默认上下文，创建全局对象 | 代码开始执行时 |
| **函数执行上下文** | 函数被调用时创建 | 函数调用时 |
| **Eval 执行上下文** | eval 函数执行时创建 | 使用 eval 时 |

### 执行上下文的组成

```
执行上下文
├── 变量对象（VO）/ 活动对象（AO）
│   ├── 变量声明
│   ├── 函数声明
│   └── arguments 对象
├── 作用域链（Scope Chain）
│   └── 当前变量对象 + 父级作用域链
└── this 指向
```

### 执行上下文栈（Call Stack）

执行上下文栈是用来管理执行上下文的栈结构，遵循 **LIFO**（后进先出）原则。

```javascript
// 执行上下文栈示例
function a() {
  console.log('a');
  b();
}

function b() {
  console.log('b');
  c();
}

function c() {
  console.log('c');
}

a();

// 执行上下文栈变化：
// 1. [全局上下文]
// 2. [全局上下文, a上下文]
// 3. [全局上下文, a上下文, b上下文]
// 4. [全局上下文, a上下文, b上下文, c上下文]
// 5. c 执行完毕，弹出
// 6. b 执行完毕，弹出
// 7. a 执行完毕，弹出
// 8. 只剩 [全局上下文]
```

### 代码示例

```javascript
// 全局执行上下文
var globalVar = '全局变量';

function outer() {
  // outer 函数执行上下文
  var outerVar = 'outer变量';
  
  function inner() {
    // inner 函数执行上下文
    var innerVar = 'inner变量';
    console.log(globalVar, outerVar, innerVar);
  }
  
  inner();
}

outer();
```

---

### 6. 执行上下文的生命周期是怎样的？

**回答：**

执行上下文的生命周期分为两个阶段：**创建阶段** 和 **执行阶段**。

### 生命周期流程

```
创建阶段 → 执行阶段 → 销毁阶段
```

### 1. 创建阶段

在代码执行之前，JavaScript 引擎会进行以下操作：

```javascript
// 示例代码
function fn(a, b) {
  var c = 3;
  function inner() {}
}

fn(1, 2);

// 创建阶段 fn 的执行上下文：
// {
//   AO: {
//     a: 1,           // 形参赋值
//     b: 2,           // 形参赋值
//     c: undefined,   // 变量声明提升
//     inner: function // 函数声明提升
//   },
//   ScopeChain: [AO(fn), 全局VO],
//   this: window
// }
```

#### 创建阶段的具体步骤：

| 步骤 | 操作 | 说明 |
|------|------|------|
| 1 | 创建变量对象（VO/AO） | 存储变量、函数声明、arguments |
| 2 | 建立作用域链 | 链接当前作用域和父级作用域 |
| 3 | 确定 this 指向 | 根据调用方式确定 |

#### 变量对象的创建顺序：

```javascript
function example(a, b) {
  var c = 10;
  function d() {}
  var e = function() {};
}

// 创建阶段（按顺序）：
// 1. 创建 arguments 对象
// 2. 扫描函数声明：d = function d() {}
// 3. 扫描变量声明：c = undefined, e = undefined
//    （注意：e 不会被赋值为函数，因为是函数表达式）
```

### 2. 执行阶段

代码开始逐行执行，变量被赋值，函数被执行。

```javascript
function fn() {
  console.log(a); // undefined（创建阶段已声明）
  var a = 1;
  console.log(a); // 1（执行阶段赋值）
}

fn();
```

### 3. 销毁阶段

函数执行完毕后，执行上下文从栈中弹出，等待垃圾回收。

```javascript
function outer() {
  let count = 0;
  
  function inner() {
    count++;
    return count;
  }
  
  return inner;
}

const fn = outer();
// outer 的执行上下文已销毁
// 但 count 变量被闭包引用，不会被回收

console.log(fn()); // 1
console.log(fn()); // 2
```

### 完整生命周期示例

```javascript
console.log(a); // undefined（变量提升）
console.log(b); // function b() {}

var a = 1;
function b() {
  console.log('b');
}
console.log(a); // 1

// 实际执行过程：
// 1. 全局上下文创建阶段：
//    - a = undefined
//    - b = function b() {}
// 2. 全局上下文执行阶段：
//    - 输出 undefined
//    - 输出 function b() {}
//    - a = 1（赋值）
//    - 输出 1
```

---

### 7. 什么是闭包？闭包的形成条件是什么？

**回答：**

闭包（Closure）是指**有权访问另一个函数作用域中变量的函数**。简单来说，闭包就是函数内部定义的函数，它可以访问外部函数的变量。

### 闭包的形成条件

1. **函数嵌套**：在一个函数内部定义另一个函数
2. **内部函数引用外部变量**：内部函数使用了外部函数的变量
3. **外部函数执行完毕**：外部函数执行后，内部函数被返回或引用

### 闭包的原理

```javascript
function outer() {
  let count = 0;  // 外部函数的变量
  
  function inner() {  // 内部函数
    count++;        // 引用外部变量
    console.log(count);
  }
  
  return inner;     // 返回内部函数
}

const fn = outer();   // outer 执行完毕，但 count 没有被销毁
fn(); // 1
fn(); // 2
fn(); // 3
```

### 闭包的本质

闭包的本质是**作用域链的延续**。即使外部函数执行完毕，其变量对象仍然被内部函数引用，因此不会被垃圾回收。

```javascript
function createCounter() {
  let count = 0;
  
  return {
    increment: function() {
      count++;
      return count;
    },
    decrement: function() {
      count--;
      return count;
    },
    getCount: function() {
      return count;
    }
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount());  // 2
```

---

### 8. 闭包有哪些应用场景？

**回答：**

### 1. 数据私有化（封装）

```javascript
function createPerson(name) {
  let age = 0;  // 私有变量
  
  return {
    getName: () => name,
    getAge: () => age,
    setAge: (newAge) => {
      if (newAge > 0) age = newAge;
    }
  };
}

const person = createPerson('Tom');
console.log(person.getName()); // 'Tom'
person.setAge(20);
console.log(person.getAge());  // 20
// age 无法从外部直接访问
```

### 2. 函数工厂（柯里化）

```javascript
function multiply(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiply(2);
const triple = multiply(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### 3. 防抖和节流

```javascript
// 防抖
function debounce(fn, delay) {
  let timer = null;
  return function(...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

// 节流
function throttle(fn, delay) {
  let lastTime = 0;
  return function(...args) {
    const now = Date.now();
    if (now - lastTime > delay) {
      lastTime = now;
      fn.apply(this, args);
    }
  };
}
```

### 4. 缓存/记忆化

```javascript
function memoize(fn) {
  const cache = {};
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache[key]) {
      console.log('从缓存读取');
      return cache[key];
    }
    const result = fn.apply(this, args);
    cache[key] = result;
    return result;
  };
}

const fibonacci = memoize(function(n) {
  if (n < 2) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(40)); // 第一次计算较慢
console.log(fibonacci(40)); // 第二次从缓存读取，很快
```

### 5. 循环中的异步问题

```javascript
// 错误示例
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 100); // 输出 5,5,5,5,5
}

// 使用闭包解决
for (var i = 0; i < 5; i++) {
  (function(j) {
    setTimeout(() => console.log(j), 100); // 输出 0,1,2,3,4
  })(i);
}

// 或使用 let
for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 100); // 输出 0,1,2,3,4
}
```

---

### 9. 闭包会导致内存泄漏吗？如何避免？

**回答：**

### 闭包与内存泄漏

**闭包本身不会导致内存泄漏**，但如果使用不当，可能会造成内存无法及时释放。

### 什么情况会造成内存泄漏

```javascript
// 1. 闭包引用大量数据
function heavyClosure() {
  const largeData = new Array(1000000).fill('x'); // 大数据
  
  return function() {
    // 只使用了 largeData 的一小部分
    console.log(largeData[0]);
  };
}

const fn = heavyClosure();
// largeData 整个数组都无法被回收，即使只使用了第一个元素

// 2. 意外的全局变量
function leak() {
  leakedVar = 'I am leaked'; // 没有声明，变成全局变量
}

// 3. DOM 引用
function setupHandler() {
  const element = document.getElementById('button');
  element.onclick = function() {
    // 这个闭包引用了 element
    console.log(element.id);
  };
}
```

### 如何避免内存泄漏

```javascript
// 1. 及时解除引用
function createClosure() {
  let data = 'sensitive data';
  
  const fn = function() {
    console.log(data);
  };
  
  // 使用完后解除引用
  data = null;
  return fn;
}

// 2. 使用 WeakMap/WeakSet
let key = { id: 1 };
const weakMap = new WeakMap();
weakMap.set(key, 'some value');

key = null; // key 可以被垃圾回收

// 3. 避免循环引用
function safeClosure() {
  let count = 0;
  
  function inner() {
    count++;
    return count;
  }
  
  return inner;
}

// 4. 使用完闭包后手动释放
const counter = createCounter();
// ... 使用 counter ...
// counter = null; // 不再需要时设为 null
```

### 现代浏览器的优化

现代浏览器对闭包有优化，只保留被引用的变量：

```javascript
function optimized() {
  const a = 'a';  // 被引用，保留
  const b = 'b';  // 未被引用，可以回收
  
  return function() {
    return a;
  };
}
```

---

### 10. 什么是立即执行函数（IIFE）？有什么作用？

**回答：**

立即执行函数表达式（Immediately Invoked Function Expression，IIFE）是一种在定义后立即执行的函数。

### 基本语法

```javascript
// 方式1：括号包裹函数表达式
(function() {
  console.log('立即执行');
})();

// 方式2：括号包裹调用
(function() {
  console.log('立即执行');
}());

// 带参数的 IIFE
(function(name) {
  console.log('Hello, ' + name);
})('World');

// 箭头函数版本
(() => {
  console.log('箭头函数 IIFE');
})();
```

### IIFE 的作用

#### 1. 创建独立的作用域

```javascript
// 避免污染全局命名空间
(function() {
  var privateVar = '私有变量';
  function privateFn() {
    console.log(privateVar);
  }
  
  // 暴露需要公开的方法
  window.myModule = {
    publicMethod: privateFn
  };
})();

// 外部无法访问 privateVar
console.log(typeof privateVar); // 'undefined'
myModule.publicMethod(); // '私有变量'
```

#### 2. 保存循环中的变量状态

```javascript
// ES5 中使用 IIFE 解决循环问题
for (var i = 0; i < 5; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // 0, 1, 2, 3, 4
    }, 100);
  })(i);
}
```

#### 3. 模块化模式

```javascript
const myModule = (function() {
  // 私有变量和方法
  let count = 0;
  
  function increment() {
    count++;
  }
  
  function getCount() {
    return count;
  }
  
  // 公开接口
  return {
    increment: increment,
    getCount: getCount
  };
})();

myModule.increment();
console.log(myModule.getCount()); // 1
```

#### 4. 执行一次性初始化

```javascript
const config = (function() {
  const settings = {
    apiUrl: 'https://api.example.com',
    timeout: 5000
  };
  
  // 从 localStorage 读取用户配置并合并
  const userConfig = JSON.parse(localStorage.getItem('config') || '{}');
  
  return { ...settings, ...userConfig };
})();
```

---

### 11. IIFE与块级作用域的关系是什么？

**回答：**

在 ES6 之前，JavaScript 只有全局作用域和函数作用域，没有块级作用域。IIFE 被用来模拟块级作用域。

### ES5 时代：用 IIFE 模拟块级作用域

```javascript
// 没有块级作用域的问题
var count = 0;

if (true) {
  var count = 10; // 覆盖了外部的 count
}

console.log(count); // 10

// 使用 IIFE 创建块级作用域
var count = 0;

(function() {
  var count = 10; // 不影响外部的 count
  console.log(count); // 10
})();

console.log(count); // 0
```

### ES6 之后：原生块级作用域

```javascript
// let/const 提供了真正的块级作用域
let count = 0;

if (true) {
  let count = 10; // 块级变量，不影响外部
  console.log(count); // 10
}

console.log(count); // 0

// 循环中的块级作用域
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 0, 1, 2
}
// 每个迭代都有自己的 i
```

### 对比

| 特性 | IIFE（ES5） | let/const（ES6+） |
|------|-------------|-------------------|
| 语法 | 较复杂 | 简洁 |
| 可读性 | 一般 | 好 |
| 性能 | 创建函数开销 | 无额外开销 |
| 使用场景 | 兼容性要求高的环境 | 现代浏览器环境 |

### 现代开发建议

```javascript
// ✅ 推荐：使用 let/const
{
  let temp = 'temporary';
  console.log(temp);
}
// temp 在这里不可访问

// ⚠️ 仅在需要兼容性时使用 IIFE
(function() {
  var temp = 'temporary';
  console.log(temp);
})();
```

---

### 12. 如何理解"函数是一等公民"？

**回答：**

"函数是一等公民"（First-Class Function）是指函数在编程语言中享有与其他数据类型（如数字、字符串、对象）同等的地位。

### 一等公民的四个特征

#### 1. 函数可以赋值给变量

```javascript
const sayHello = function() {
  console.log('Hello');
};

const greet = sayHello;
greet(); // 'Hello'
```

#### 2. 函数可以作为参数传递

```javascript
function execute(fn) {
  fn();
}

execute(function() {
  console.log('被执行了');
});

// 实际应用：回调函数
setTimeout(() => console.log('1秒后执行'), 1000);

// 数组方法
[1, 2, 3].map(x => x * 2); // [2, 4, 6]
```

#### 3. 函数可以作为返回值

```javascript
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = createMultiplier(2);
console.log(double(5)); // 10
```

#### 4. 函数可以存储在数据结构中

```javascript
const operations = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b,
  multiply: (a, b) => a * b,
  divide: (a, b) => a / b
};

console.log(operations.add(2, 3));      // 5
console.log(operations.multiply(4, 5)); // 20

// 函数数组
const pipeline = [
  x => x + 1,
  x => x * 2,
  x => x - 3
];

const result = pipeline.reduce((acc, fn) => fn(acc), 5);
console.log(result); // (5 + 1) * 2 - 3 = 9
```

### 函数是一等公民的意义

1. **高阶函数**：函数可以操作其他函数
2. **函数式编程**：支持 map、filter、reduce 等函数式操作
3. **回调和异步**：支持异步编程模式
4. **代码复用**：通过函数组合实现代码复用

### 实际应用示例

```javascript
// 函数组合
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);

const add1 = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

const composed = compose(square, double, add1);
console.log(composed(2)); // ((2 + 1) * 2)^2 = 36

// 装饰器模式
function withLogging(fn) {
  return function(...args) {
    console.log(`调用 ${fn.name}，参数：`, args);
    const result = fn.apply(this, args);
    console.log(`返回：`, result);
    return result;
  };
}

function add(a, b) {
  return a + b;
}

const loggedAdd = withLogging(add);
loggedAdd(2, 3);
// 调用 add，参数： [2, 3]
// 返回： 5
```

---

## 1.2 this/原型链/继承（10道）

### 13. this的绑定规则有哪些？分别是什么？

**待回答**

---

### 14. 箭头函数的this指向哪里？与普通函数有何区别？

**待回答**

---

### 15. call、apply、bind的区别是什么？

**待回答**

---

### 16. 如何实现一个自己的bind函数？

**待回答**

---

### 17. 什么是原型？什么是原型链？

**待回答**

---

### 18. 原型链的查找机制是怎样的？

**待回答**

---

### 19. 如何实现继承？ES5有哪些继承方式？

**待回答**

---

### 20. ES6的class语法糖背后是什么原理？

**待回答**

---

### 21. 寄生组合继承的实现原理是什么？

**待回答**

---

### 22. 如何理解constructor属性？

**待回答**

---

## 1.3 异步编程（15道）

### 23. 什么是事件循环（Event Loop）？

**待回答**

---

### 24. 宏任务和微任务分别有哪些？执行顺序是怎样的？

**待回答**

---

### 25. setTimeout/setInterval的执行时机是怎样的？

**待回答**

---

### 26. Promise是什么？解决了什么问题？

**待回答**

---

### 27. Promise的三种状态是什么？如何转换？

**待回答**

---

### 28. Promise.then()的链式调用原理是什么？

**待回答**

---

### 29. Promise.all()和Promise.race()的区别？

**待回答**

---

### 30. Promise.allSettled()和Promise.any()的用法？

**待回答**

---

### 31. async/await的原理是什么？

**待回答**

---

### 32. async/await与Promise的关系是什么？

**待回答**

---

### 33. async/await如何捕获异常？

**待回答**

---

### 34. 如何实现Promise的串行执行？

**待回答**

---

### 35. 如何实现Promise的并行控制并发数？

**待回答**

---

### 36. 什么是协程？Generator函数与异步编程的关系？

**待回答**

---

### 37. for await...of的用法是什么？

**待回答**

---

## 1.4 ES6+深入（12道）

### 38. let和const的区别是什么？

**待回答**

---

### 39. 解构赋值的原理是什么？

**待回答**

---

### 40. 模板字符串的原理是什么？

**待回答**

---

### 41. ES6模块与CommonJS模块的区别？

**待回答**

---

### 42. Proxy代理可以拦截哪些操作？

**待回答**

---

### 43. Reflect对象的作用是什么？

**待回答**

---

### 44. Map和Set的区别？与Object的区别？

**待回答**

---

### 45. WeakMap和WeakSet的特点是什么？

**待回答**

---

### 46. 迭代器和生成器的概念是什么？

**待回答**

---

### 47. Symbol类型的用途有哪些？

**待回答**

---

### 48. 可选链操作符?.和空值合并运算符??的用法？

**待回答**

---

### 49. ES2020+有哪些新特性值得关注？

**待回答**

---

## 1.5 类型/转换/判断（6道）

### 50. ==和===的区别是什么？

**待回答**

---

### 51. JavaScript的隐式类型转换规则有哪些？

**待回答**

---

### 52. typeof操作符的缺陷是什么？

**待回答**

---

### 53. 如何判断一个变量的具体类型？

**待回答**

---

### 54. Object.prototype.toString.call()的原理？

**待回答**

---

### 55. 如何判断一个对象是空对象？

**待回答**

---
