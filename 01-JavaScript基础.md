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

执行上下文的生命周期分为三个阶段：**创建阶段** → **执行阶段** → **销毁阶段**。

### 1. 创建阶段

在代码执行前，JavaScript 引擎会进行：
- **创建变量对象（VO/AO）**：存储变量、函数声明、arguments
- **建立作用域链**：链接当前作用域和父级作用域
- **确定 this 指向**：根据调用方式确定

```javascript
function fn(a, b) {
  var c = 3;
  function inner() {}
}

fn(1, 2);

// 创建阶段 fn 的执行上下文：
// AO: {
//   a: 1,           // 形参赋值
//   b: 2,           // 形参赋值
//   c: undefined,   // 变量声明提升
//   inner: function // 函数声明提升
// }
```

### 2. 执行阶段

代码逐行执行，变量被赋值。

```javascript
function fn() {
  console.log(a); // undefined（创建阶段已声明）
  var a = 1;      // 执行阶段赋值
  console.log(a); // 1
}
fn();
```

### 3. 销毁阶段

函数执行完毕，执行上下文从栈中弹出。

```javascript
function outer() {
  let count = 0;
  function inner() {
    count++;
    return count;
  }
  return inner;
}

const fn = outer(); // outer 上下文销毁，但 count 被闭包引用保留
console.log(fn());  // 1
console.log(fn());  // 2
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

## 1.2 this/原型链/继承（10道）

### 13. this的绑定规则有哪些？分别是什么？

**回答：**

### this 的 5 种绑定规则

| 规则 | 说明 | this 指向 |
|------|------|----------|
| 默认绑定 | 函数独立调用 | 全局对象（浏览器window） |
| 隐式绑定 | 作为对象方法调用 | 调用该函数的对象 |
| 显式绑定 | call/apply/bind | 第一个参数 |
| new 绑定 | new 关键字调用 | 新创建的实例对象 |
| 箭头函数 | 箭头函数 | 外层作用域的 this |

### 代码示例

```javascript
// 1. 默认绑定
function fn1() {
  console.log(this); // window（浏览器）
}
fn1();

// 2. 隐式绑定
const obj = {
  name: '张三',
  fn2() {
    console.log(this.name); // '张三'
  }
};
obj.fn2();

// 3. 显式绑定
function fn3() {
  console.log(this.name);
}
const obj1 = { name: '李四' };
fn3.call(obj1); // '李四'

// 4. new 绑定
function Person(name) {
  this.name = name;
}
const p = new Person('王五');
console.log(p.name); // '王五'
```

---

### 14. 箭头函数的this指向哪里？与普通函数有何区别？

**回答：**

### 箭头函数的 this

箭头函数**没有自己的 this**，它的 this 继承自**外层作用域**的 this。

### 与普通函数的区别

| 特性 | 普通函数 | 箭头函数 |
|------|----------|----------|
| this 来源 | 动态绑定 | 继承外层作用域 |
| call/apply/bind | 可以改变 this | 无法改变 this |
| new 绑定 | 可以作为构造函数 | 不能作为构造函数 |
| arguments | 有 arguments 对象 | 没有 arguments |
| 原型 | 有 prototype | 没有 prototype |

### 代码示例

```javascript
// 箭头函数 this 继承外层
const obj = {
  name: '张三',
  fn() {
    setTimeout(() => {
      console.log(this.name); // '张三'（继承自外层）
    }, 100);
    
    setTimeout(function() {
      console.log(this.name); // undefined（默认绑定）
    }, 100);
  }
};
obj.fn();

// 箭头函数无法通过 call 改变 this
const fn = () => console.log(this);
fn.call({ name: '李四' }); // 还是指向外层作用域
```

---

### 15. call、apply、bind的区别是什么？

**回答：**

### 三者对比

| 方法 | 参数 | 执行方式 | 常用场景 |
|------|------|----------|----------|
| call | thisArg, param1, param2, ... | 立即执行 | 借用方法、指定 this |
| apply | thisArg, [params] | 立即执行 | 数组参数、借用方法 |
| bind | thisArg, param1, param2, ... | 返回新函数 | 偏函数、延迟执行 |

### 代码示例

```javascript
function sum(a, b) {
  return a + b;
}

// call：参数逐个传递
console.log(sum.call(null, 1, 2)); // 3

// apply：参数数组
console.log(sum.apply(null, [1, 2])); // 3

// bind：返回新函数
const add1 = sum.bind(null, 1);
console.log(add1(2)); // 3

// 借用方法
const arr = [1, 2, 3];
Math.max.apply(null, arr); // 3
```

---

### 16. 如何实现一个自己的bind函数？11

**回答：**

### 简易版 bind

```javascript
Function.prototype.myBind = function(context, ...args1) {
  const fn = this;
  return function(...args2) {
    return fn.apply(context, [...args1, ...args2]);
  };
};
```

### 完整版 bind（支持 new）

```javascript
Function.prototype.myBind = function(context, ...args1) {
  const fn = this;
  
  // 中间函数用于处理原型链
  function F() {}
  
  function boundFn(...args2) {
    const ctx = this instanceof F ? this : context;
    return fn.apply(ctx, [...args1, ...args2]);
  }
  
  F.prototype = fn.prototype;
  boundFn.prototype = new F();
  
  return boundFn;
};
```

### 测试代码

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const obj = {};
const BoundPerson = Person.myBind(obj, '张三');
const p = new BoundPerson(20);
console.log(obj.name); // undefined（new 时忽略绑定的 context）
console.log(p.name); // '张三'
```

---

### 17. 什么是原型？什么是原型链？

**回答：**

### 原型（Prototype）

每个函数都有一个 `prototype` 属性，指向一个对象。这个对象就是所有通过该构造函数创建的实例的**原型**。

### 原型链（Prototype Chain）

当访问对象的属性时，如果对象本身没有，就会去它的原型对象上找，如果还没有，就继续找原型的原型，直到找到 `null`，这就是**原型链**。

### 代码示例

```javascript
// 构造函数
function Person(name) {
  this.name = name;
}

// 原型上添加方法
Person.prototype.sayHi = function() {
  console.log('Hi, ' + this.name);
};

// 创建实例
const p = new Person('张三');
p.sayHi(); // 'Hi, 张三'

// 原型链关系
console.log(p.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__ === null); // true
```

---

### 18. 原型链的查找机制是怎样的？

**回答：**

### 查找过程

1. **对象本身** → 找到则返回
2. **对象的 `__proto__`** → 原型对象 → 找到则返回
3. **原型对象的 `__proto__`** → 继续向上查找
4. **直到 `null`** → 找不到返回 `undefined`

### 代码示例

```javascript
function Person() {}
Person.prototype.a = 1;

function Student() {}
Student.prototype = new Person();
Student.prototype.b = 2;

const s = new Student();
s.c = 3;

// 查找属性
console.log(s.c); // 3（对象本身）
console.log(s.b); // 2（Student.prototype）
console.log(s.a); // 1（Person.prototype）
console.log(s.d); // undefined（到 null 都没找到）

// hasOwnProperty 只检查对象本身
console.log(s.hasOwnProperty('c')); // true
console.log(s.hasOwnProperty('a')); // false
```

---

### 19. 如何实现继承？ES5有哪些继承方式？

**回答：**

### ES5 继承方式

| 方式 | 优点 | 缺点 |
|------|------|------|
| 原型链继承 | 简单易实现 | 所有实例共享原型 |
| 构造函数继承 | 不共享原型 | 无法继承原型方法 |
| 组合继承 | 结合两者优点 | 调用两次父构造函数 |
| 寄生组合继承 | 最优方案 | 实现稍复杂 |

### 1. 原型链继承

```javascript
function Parent() {
  this.name = 'parent';
}

Parent.prototype.say = function() {
  console.log('say');
};

function Child() {}
Child.prototype = new Parent();

const c = new Child();
c.say(); // 'say'
```

### 2. 构造函数继承

```javascript
function Parent(name) {
  this.name = name;
}

function Child(name) {
  Parent.call(this, name);
}

const c = new Child('child');
console.log(c.name); // 'child'
```

### 3. 组合继承

```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.say = function() {
  console.log(this.name);
};

function Child(name) {
  Parent.call(this, name);
}

Child.prototype = new Parent();
Child.prototype.constructor = Child;

const c = new Child('child');
c.say(); // 'child'
```

---

### 20. ES6的class语法糖背后是什么原理？

**回答：**

ES6 的 `class` 本质上是 **ES5 寄生组合继承的语法糖**。

### class 转 ES5 示例

```javascript
// ES6 class
class Person {
  constructor(name) {
    this.name = name;
  }
  
  sayHi() {
    console.log('Hi, ' + this.name);
  }
}

// 等价于 ES5 实现
function Person(name) {
  this.name = name;
}

Person.prototype.sayHi = function() {
  console.log('Hi, ' + this.name);
};
```

### extends 原理

```javascript
// ES6
class Student extends Person {
  constructor(name, grade) {
    super(name);
    this.grade = grade;
  }
}

// 等价于：寄生组合继承
function Student(name, grade) {
  Person.call(this, name);
  this.grade = grade;
}

Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;
```

---

### 21. 寄生组合继承的实现原理是什么？

**回答：**

### 核心思想

1. **借用父构造函数**：继承实例属性
2. **原型继承**：继承原型方法（但不创建多余父实例）

### 实现代码

```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.say = function() {
  console.log(this.name);
};

function Child(name, age) {
  Parent.call(this, name); // 继承实例属性
  this.age = age;
}

// 关键：使用 Object.create 继承原型
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

const c = new Child('child', 10);
c.say(); // 'child'
```

### 为什么最优？

| 问题 | 组合继承 | 寄生组合继承 |
|------|----------|--------------|
| 父构造函数调用次数 | 2 次 | 1 次 |
| 子类原型上有无父属性 | 有 | 无 |
| 性能 | 较差 | 更好 |

---

### 22. 如何理解constructor属性？

**回答：**

### constructor 是什么

每个**原型对象**都有一个 `constructor` 属性，指向**对应的构造函数**。

### 代码示例

```javascript
function Person() {}

console.log(Person.prototype.constructor === Person); // true

const p = new Person();
console.log(p.constructor === Person); // true（实例继承自原型）
```

### 继承时需要修正 constructor

```javascript
function Parent() {}
function Child() {}

// 原型继承
Child.prototype = new Parent();
console.log(Child.prototype.constructor === Parent); // true（错误）

// 修正 constructor
Child.prototype.constructor = Child;
console.log(Child.prototype.constructor === Child); // true（正确）
```

### constructor 的作用

1. **标识类型**：`instance.constructor` 可以判断实例类型
2. **克隆对象**：可以通过 `new obj.constructor()` 克隆对象

```javascript
function Person(name) {
  this.name = name;
}

const p1 = new Person('张三');
const p2 = new p1.constructor('李四');
console.log(p2.name); // '李四'
```

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
