# JavaScript基础（55道）

## 1.1 作用域/闭包/执行上下文（12道）

### 1. 什么是作用域？JavaScript有哪些作用域类型？

**回答：**

变量和函数的可访问范围。

| 类型 | 作用域 |
|------|--------|
| 全局 | 任何地方 |
| 函数 | 函数内部 |
| 块级 | `{}` 内部（let/const）|
| 模块 | ES6 模块内部 |

<!-- ### 代码示例

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

--- -->

### 2. 什么是作用域链？如何理解其作用？

**回答：**

变量查找机制：**先在当前作用域找 → 找不到去父级作用域找 → 直到全局 → 找不到返回 undefined**。

```javascript
const a = 1;
function outer() {
  const b = 2;
  function inner() {
    console.log(a, b); // inner → outer → 全局
  }
  inner();
}

---

### 3. 什么是变量提升？let/const/var的提升行为有何不同？

**回答：**

| 声明 | 提升 | 初始值 | 暂时性死区 |
|------|------|--------|-----------|
| var | ✅ | undefined | ❌ |
| let | ✅ | 无（报错）| ✅ |
| const | ✅ | 无（报错）| ✅ |

```javascript
// var
console.log(a); // undefined
var a = 1;

// let/const - 报错
console.log(b); // ReferenceError
let b = 2;
```

---

### 4. 什么是暂时性死区（TDZ）？

**回答：**

`let/const` 在声明前不可访问的区域（从块开始到声明处）。

```javascript
{
  console.log(x); // TDZ 报错
  let x = 1;
}
```

---

### 5. 什么是执行上下文？执行上下文栈是什么？11

**回答：**

代码执行的环境。执行上下文栈（调用栈）管理多个执行上下文，遵循 **LIFO**。

```javascript
function a() { b(); }
function b() { c(); }
function c() {}

// 栈：全局 → a → b → c → 弹出 c → b → a → 全局
a();
```

---

### 6. 执行上下文的生命周期是怎样的？11

**回答：**

三个阶段：
1. **创建**：创建变量对象、建立作用域链、确定 this
2. **执行**：逐行执行代码
3. **销毁**：函数执行完弹出栈

---

### 7. 什么是闭包？闭包的形成条件是什么？

**回答：**

函数能访问**外部函数作用域**的变量。

**条件**：函数嵌套 + 内部函数引用外部变量 + 内部函数被返回/引用

```javascript
function outer() {
  let count = 0;
  return function() { return ++count; };
}
const fn = outer();
fn(); // 1
fn(); // 2
```

---

### 8. 闭包有哪些应用场景？

**回答：**

- **私有变量**：封装
- **函数工厂**：柯里化
- **防抖/节流**
- **循环异步问题**：`var` 用 IIFE 或 `let`

---

### 9. 闭包会导致内存泄漏吗？如何避免？

**回答：**

闭包本身不会，但注意：
- 避免引用大数据
- 及时解除引用
- 少用全局变量

---

### 10. 什么是立即执行函数（IIFE）？有什么作用？

**回答：**

定义后立即执行的函数，**创建独立作用域**。

```javascript
(function() {
  var x = 1; // 外部访问不到
})();
```

作用：模拟块级作用域（ES6 前）、模块化、避免污染全局。

---

### 11. IIFE与块级作用域的关系是什么？

**回答：**

ES6 前用 IIFE 模拟块级作用域，ES6 后用 `let/const`。

---

### 12. 如何理解"函数是一等公民"？11

**回答：**

函数可以：
- 赋值给变量
- 作为参数传递
- 作为返回值
- 存储在数据结构中

```javascript
const fn = () => {}; // 赋值
[1,2].map(fn);      // 参数
() => () => {};     // 返回
```

---

## 1.2 this/原型链/继承（10道）

### 13. this的绑定规则有哪些？分别是什么？

**回答：**

| 规则 | 场景 | this 指向 |
|------|------|-----------|
| 默认绑定 | 独立调用 | window |
| 隐式绑定 | obj.fn() | obj |
| 显式绑定 | fn.call(obj) | 第一个参数 |
| new 绑定 | new Person() | 新实例 |
| 箭头函数 | () => {} | 外层 this |

```javascript
fn();           // window
obj.fn();       // obj
fn.call(obj);   // obj
new Fn();       // 新实例
```

---

### 14. 箭头函数的this指向哪里？与普通函数有何区别？

**回答：**

没有自己的 this，**继承外层作用域**的 this。

| 特性 | 普通函数 | 箭头函数 |
|------|----------|----------|
| this | 动态绑定 | 继承外层 |
| call/apply | 可改 | 不可改 |
| constructor | ✅ | ❌ |
| arguments | ✅ | ❌ |

```javascript
const obj = {
  name: 'a',
  fn() {
    setTimeout(() => console.log(this.name)); // 'a'，继承外层
    setTimeout(function() { console.log(this.name); }); // undefined
  }
};
```

---

### 15. call、apply、bind的区别是什么？

**回答：**

| 方法 | 参数 | 执行 |
|------|------|------|
| call | this, a, b, ... | 立即 |
| apply | this, [a, b] | 立即 |
| bind | this, a, b, ... | 返回新函数 |

```javascript
fn.call(obj, 1, 2);    // 立即执行
fn.apply(obj, [1, 2]);  // 立即执行，数组参数
fn.bind(obj, 1)(2);     // 返回新函数
```

---

### 16. 如何实现一个自己的bind函数？

**回答：**

```javascript
Function.prototype.myBind = function(context, ...args1) {
  const fn = this;
  return function(...args2) {
    return fn.apply(context, [...args1, ...args2]);
  };
};
```

---

### 17. 什么是原型？什么是原型链？

**回答：**

每个函数有 `prototype`，实例通过 `__proto__` 访问。

**原型链**：对象 → 原型 → 原型的原型 → ... → null

```javascript
function Person(name) { this.name = name; }
Person.prototype.say = function() { console.log(this.name); };

const p = new Person('a');
p.say(); // 'a'

// 原型链
p.__proto__ === Person.prototype
Person.prototype.__proto__ === Object.prototype
Object.prototype.__proto__ === null
```

---

### 18. 原型链的查找机制是怎样的？

**回答：**

对象本身 → `__proto__` → 原型的原型 → ... → null

找不到返回 `undefined`

```javascript
function Parent() {}
Parent.prototype.a = 1;

function Child() {}
Child.prototype = new Parent();
Child.prototype.b = 2;

const c = new Child();
c.c = 3;

c.c; // 3（自身）
c.b; // 2（Child.prototype）
c.a; // 1（Parent.prototype）
c.d; // undefined
```

---

### 19. 如何实现继承？ES5有哪些继承方式？

**回答：**

| 方式 | 原理 |
|------|------|
| 原型链 | Child.prototype = new Parent() |
| 构造函数 | Parent.call(this) |
| 组合 | 两者结合 |
| 寄生组合 | Object.create + call（最优）|

```javascript
// 寄生组合继承（最优）
function Parent(name) { this.name = name; }
Parent.prototype.say = function() { console.log(this.name); };

function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```

---

### 20. ES6的class语法糖背后是什么原理？

**回答：**

ES6 `class` 是 **寄生组合继承** 的语法糖。

```javascript
// class
class Person {
  constructor(name) { this.name = name; }
  say() { console.log(this.name); }
}

// 等价 ES5
function Person(name) { this.name = name; }
Person.prototype.say = function() { console.log(this.name); };

// extends 也是寄生组合继承
class Student extends Person {}
// 等价
Student.prototype = Object.create(Person.prototype);
```

---

### 21. 寄生组合继承的实现原理是什么？

**回答：**

1. `Parent.call(this)`：继承实例属性
2. `Object.create(Parent.prototype)`：继承原型方法（不创建多余父实例）

```javascript
function Parent(name) { this.name = name; }
function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```

**最优**：只调用1次父构造函数，子类原型无多余属性。

---

### 22. 如何理解constructor属性？

**回答：**

原型对象上的属性，指向**构造函数**。

```javascript
function Person() {}
Person.prototype.constructor === Person; // true

// 继承时需修正
Child.prototype = new Parent();
Child.prototype.constructor = Child; // 修正回 Child
```

### constructor 的作用

1. **标识类型**：判断实例类型
2. **克隆对象**：`new obj.constructor()`

---

## 1.3 异步编程（15道）

### 23. 什么是事件循环（Event Loop）？

**回答：**

**执行栈 → 微任务 → 宏任务 → 下一轮**

```javascript
console.log(1);           // 同步
setTimeout(() => console.log(2), 0); // 宏任务
Promise.resolve().then(() => console.log(3)); // 微任务
console.log(4);           // 同步
// 输出：1, 4, 3, 2
```

---

### 24. 宏任务和微任务分别有哪些？执行顺序是怎样的？

**回答：**

| 宏任务 | 微任务 |
|--------|--------|
| setTimeout、setInterval、I/O | Promise.then、async/await、queueMicrotask |

**顺序**：微任务 > 宏任务

```javascript
setTimeout(() => console.log(1), 0);
new Promise(r => { r(); console.log(2); }).then(() => console.log(3));
console.log(4);
// 输出：2, 4, 3, 1
```

---

### 25. setTimeout/setInterval的执行时机是怎样的？

**回答：**

延迟指定时间后**加入宏任务队列**，主线程空闲才执行。

```javascript
setTimeout(() => console.log(1), 100);
// 100ms 后加入宏任务，线程空闲时执行
```

---

### 26. Promise是什么？解决了什么问题？

**回答：**

异步编程解决方案，解决**回调地狱**。

```javascript
new Promise((resolve, reject) => {
  setTimeout(() => resolve('成功'), 1000);
}).then(res => console.log(res));
```

---

### 27. Promise的三种状态是什么？如何转换？

**回答：**

| 状态 | 说明 |
|------|------|
| pending | 进行中 |
| fulfilled | 已成功 |
| rejected | 已失败 |

**状态不可逆**：pending → fulfilled 或 rejected

```javascript
new Promise(r => r('第一次')).then(console.log); // 第一次
```

---

### 28. Promise.then()的链式调用原理是什么？

**回答：**

.then() 返回**新 Promise**，实现链式。

| 返回值 | 下个 then 接收 |
|--------|----------------|
| 非 Promise | 值本身 |
| Promise | 等待落定 |
| 抛错 | rejected |

```javascript
Promise.resolve(1)
  .then(x => x + 1)
  .then(x => x * 2)
  .then(console.log); // 4
```

---

### 29. Promise.all()和Promise.race()的区别？

**回答：**

| 方法 | 说明 |
|------|------|
| all | 全部完成才成功，一个失败即失败 |
| race | 任意一个完成就结束（最快取胜）|

```javascript
Promise.all([p1, p2, p3]).then(console.log); // 全部成功
Promise.race([p1, p2]).then(console.log);    // 最快的一个
```

---

### 30. Promise.allSettled()和Promise.any()的用法？

**回答：**

- allSettled：等全部完成，返回每个结果（无论成功/失败）
- any：任一成功即可，全部失败才失败

```javascript
Promise.allSettled([p1, p2]); // [{status:'fulfilled',value:1}, {status:'rejected',reason:...}]
Promise.any([p1, p2]);       // 第一个成功的结果
```

---

### 31. async/await的原理是什么？

**回答：**

Promise + Generator 的语法糖。

```javascript
async function fn() {
  const a = await p1();
  return a;
}
// 等价
function fn() { return p1().then(a => a); }
```

---

### 32. async/await与Promise的关系是什么？

**回答：**

async 函数**返回 Promise**，await **等待** Promise 落定。

```javascript
// Promise → async/await
fetch('/api').then(res => res.json());
// 等价
const res = await fetch('/api');
```

---

### 33. async/await如何捕获异常？

**回答：**

try/catch 或 Promise.catch

```javascript
async function fn() {
  try {
    const res = await fetch('/api');
  } catch (e) {
    console.error(e);
  }
}
```

---

### 34. 如何实现Promise的串行执行？

**回答：**

```javascript
async function serial(tasks) {
  const results = [];
  for (const task of tasks) {
    results.push(await task());
  }
  return results;
}
```

---

### 35. 如何实现Promise的并行控制并发数？

**回答：**

同时启动 n 个任务，完成后补充新任务。

```javascript
async function concurrency(tasks, limit) {
  const results = [], running = [];
  for (const task of tasks) {
    const p = task().then(v => {
      results.push(v);
      running.splice(running.indexOf(p), 1);
    });
    running.push(p);
    if (running.length >= limit) await Promise.race(running);
  }
  await Promise.all(running);
  return results;
}
```

---

### 36. 什么是协程？Generator函数与异步编程的关系？

**回答：**

Generator 用 `yield` 暂停，`next()` 恢复。async/await 是其语法糖。

```javascript
function* gen() {
  yield 1;
  yield 2;
  return 3;
}
const g = gen();
g.next(); // {value:1,done:false}
g.next(); // {value:2,done:false}
g.next(); // {value:3,done:true}
```

---

### 37. for await...of的用法是什么？

**回答：**

遍历可迭代对象，**等待**每个 Promise 落定。

```javascript
async function fn() {
  for await (const v of [p1, p2, p3]) {
    console.log(v); // 依次输出 resolved 值
  }
}
```

---

## 1.4 ES6+深入（12道）

### 38. let和const的区别是什么？

**回答：**

| 区别 | let | const |
|------|-----|-------|
| 值可修改 | ✅ | ❌ |
| 初始化 | 可不初始化 | 必须初始化 |
| 其它 | 同：块级作用域、TDZ、无变量提升 | 同 |

```javascript
let a;     // 可先声明后赋值
const b;   // ❌ 报错，必须初始化
const c = 1;
c = 2;     // ❌ 报错
```

---

### 39. 解构赋值的原理是什么？

**回答：**

模式匹配，依次提取值。

```javascript
// 数组解构
const [a, b, c] = [1, 2, 3]; // a=1,b=2,c=3

// 对象解构
const { name, age } = { name: 'Tom', age: 20 };

// 剩余解构
const [a, ...rest] = [1, 2, 3]; // rest=[2,3]

// 默认值
const [x = 1] = []; // x=1
```

---

### 40. 模板字符串的原理是什么？

**回答：**

用反引号`` ``，支持插值 `${}` 和多行。

```javascript
const name = 'Tom';
const age = 20;

const str = `我是${name}，今年${age}岁`;
console.log(str); // 我是Tom，今年20岁
```

---

### 41. ES6模块与CommonJS模块的区别？

**回答：**

| 区别 | ES6模块 | CommonJS |
|------|---------|----------|
| 语法 | import/export | require/module.exports |
| 加载 | 异步编译时 | 同步运行时 |
| 导出 | 值引用（只读）| 值拷贝 |
| this | undefined | module.exports |

```javascript
// ES6
export const a = 1;
export default function() {}
import { a } from './x';
import x from './x';

// CommonJS
module.exports = {};
const x = require('./x');
```

---

### 42. Proxy代理可以拦截哪些操作？

**回答：**

13种拦截操作：

| 操作 | 说明 |
|------|------|
| get | 读取属性 |
| set | 写入属性 |
| has | in 运算符 |
| deleteProperty | delete |
| apply | 函数调用 |
| construct | new |
| getOwnPropertyDescriptor | Object.getOwnPropertyDescriptor |
| defineProperty | Object.defineProperty |
| getPrototypeOf | Object.getPrototypeOf |
| setPrototypeOf | Object.setPrototypeOf |
| preventExtensions | Object.preventExtensions |
| isExtensible | Object.isExtensible |

```javascript
const obj = { a: 1 };
const proxy = new Proxy(obj, {
  get(target, key) {
    console.log(`读取${key}`);
    return target[key];
  },
  set(target, key, val) {
    console.log(`设置${key}=${val}`);
    target[key] = val;
  }
});
proxy.a;    // 读取a
proxy.b = 2; // 设置b=2
```

---

### 43. Reflect对象的作用是什么？

**回答：**

统一的对象操作 API，替代 Object 上的方法。

```javascript
// 替代 Object 方法
Reflect.get(obj, 'key');
Reflect.set(obj, 'key', value);
Reflect.has(obj, 'key');
Reflect.deleteProperty(obj, 'key');

// 配合 Proxy
const proxy = new Proxy(obj, {
  get(target, key) {
    return Reflect.get(target, key);
  }
});
```

---

### 44. Map和Set的区别？与Object的区别？

**回答：**

| 特性 | Map | Set | Object |
|------|-----|-----|--------|
| 键 | 任意类型 | 值唯一 | 字符串/Symbol |
| 有序 | ✅ | ✅ | ✅（ES6+）|
| size | ✅ | ✅ | ❌ |
| 迭代 | 可迭代 | 可迭代 | 需 keys/values |

```javascript
// Map：键值对
const m = new Map();
m.set('a', 1);
m.get('a'); // 1

// Set：值集合（不重复）
const s = new Set([1, 2, 2, 3]); // 自动去重
s.size; // 3
```

---

### 45. WeakMap和WeakSet的特点是什么？

**回答：**

**弱引用**：不影响垃圾回收，键必须是对象。

| 特性 | WeakMap | WeakSet |
|------|---------|---------|
| 键 | 必须是对象 | 必须是对象 |
| 引用 | 弱引用 | 弱引用 |
| 遍历 | ❌ | ❌ |
| clear() | ❌ | ❌ |

```javascript
const wm = new WeakMap();
let obj = { name: 'Tom' };
wm.set(obj, 'data');
wm.get(obj); // 'data'

obj = null; // obj 可被回收，WeakMap 自动清除
```

---

### 46. 迭代器和生成器的概念是什么？

**回答：**

**迭代器**：有 `next()` 方法，返回 `{value, done}`。

**生成器**：function*，用 `yield` 暂停。

```javascript
// 迭代器
const arr = [1, 2, 3];
const it = arr[Symbol.iterator]();
it.next(); // {value:1,done:false}
it.next(); // {value:2,done:false}
it.next(); // {value:3,done:false}
it.next(); // {value:undefined,done:true}

// 生成器
function* gen() {
  yield 1;
  yield 2;
  return 3;
}
const g = gen();
g.next(); // {value:1,done:false}
```

---

### 47. Symbol类型的用途有哪些？

**回答：**

唯一标识符，用于属性名、私有成员等。

```javascript
// 创建
const s = Symbol('desc'); // 描述可省略

// 用作属性名（唯一）
const obj = {
  [Symbol('a')]: 1,
  [Symbol('a')]: 2 // 不重复
};

// 常用 Symbol
Symbol.iterator;   // 可迭代
Symbol.toStringTag; // Object.prototype.toString.call()
Symbol.for();     // 全局 Symbol 注册表
```

---

### 48. 可选链操作符?.和空值合并运算符??的用法？

**回答：**

?.：安全访问深层属性
??：左侧为 null/undefined 时用右侧值

```javascript
// 可选链
const obj = { a: { b: 1 } };
obj?.a?.b;      // 1
obj?.c?.d;      // undefined（不报错）
obj?.a?.fn?.(); // 安全调用

// 空值合并
const x = null ?? 'default';   // 'default'
const y = 0 ?? 'default';     // 0（0不是null/undefined）
const z = '' ?? 'default';    // ''
```

---

### 49. ES2020+有哪些新特性值得关注？

**回答：**

| 特性 | 作用 |
|------|------|
| optional chaining ?. | 安全访问 |
| nullish ?? | 空值合并 |
| BigInt | 大整数 |
| dynamic import | 动态导入 |
| Promise.allSettled | 全部结束 |
| String.prototype.matchAll | 迭代匹配 |
| globalThis | 全局对象 |
| ??= | 空值赋值 |

```javascript
// BigInt
const big = 9007199254740991n;

// dynamic import
const module = await import('./module.js');

// ??=
x ??= 1; // x ??= 1 等价于 x ?? (x = 1)
```

---

## 1.5 类型/转换/判断（6道）

### 50. ==和===的区别是什么？

**回答：**

- `===`：严格相等，不隐式转换
- `==`：宽松相等，会隐式转换

```javascript
1 === '1';   // false
1 == '1';    // true（转成数字比较）
null == undefined; // true
NaN === NaN; // false（NaN 不等于任何值）
```

---

### 51. JavaScript的隐式类型转换规则有哪些？

**回答：**

| 操作 | 转换规则 |
|------|----------|
| +（字符串）| 任意转字符串 |
| - * / % | 转数字 |
| == != | 转数字比较 |
| ! | 转布尔 |

```javascript
// + 字符串优先
'1' + 1;   // '11'
1 + true;  // 2（true→1）
1 + {};    // '1[object Object]'

// - 转数字
'5' - 2;   // 3
'5' * '2';  // 10

// 布尔
!!'';      // false
!!1;       // true
```

---

### 52. typeof操作符的缺陷是什么？

**回答：**

| 缺陷 | 说明 |
|------|------|
| null | typeof null → 'object' |
| 函数对象 | 无法细分 |
| 正则 | typeof /a/ → 'object' |

```javascript
typeof null;        // 'object' ❌ 历史 bug
typeof [];          // 'object' ❌ 数组也是 object
typeof function(){} // 'function' ✅
typeof /a/;        // 'object'
```

---

### 53. 如何判断一个变量的具体类型？

**回答：**

| 类型 | 判断方法 |
|------|----------|
| 原始类型 | typeof |
| 对象类型 | Object.prototype.toString.call() |
| 数组 | Array.isArray() |
| 实例 | instanceof |

```javascript
typeof 'str';     // 'string'
typeof 123;       // 'number'
typeof true;      // 'boolean'
typeof undefined; // 'undefined'
typeof null;      // 'object'（bug）

Array.isArray([]); // true

[] instanceof Array; // true
{} instanceof Object; // true
```

---

### 54. Object.prototype.toString.call()的原理？

**回答：**

调用对象的 toString 方法，获取内部 [[Class]]。

```javascript
Object.prototype.toString.call(1);      // '[object Number]'
Object.prototype.toString.call('str'); // '[object String]'
Object.prototype.toString.call(true);  // '[object Boolean]'
Object.prototype.toString.call([]);    // '[object Array]'
Object.prototype.toString.call({});    // '[object Object]'
Object.prototype.toString.call(null);  // '[object Null]'
Object.prototype.toString.call(undefined); // '[object Undefined]'
```

---

### 55. 如何判断一个对象是空对象？

**回答：**

```javascript
const obj = {};

// 方法1：Object.keys
Object.keys(obj).length === 0; // true

// 方法2：for...in
for (let k in obj) { return false; }
return true;

// 方法3：JSON.stringify（不推荐，有顺序问题）
JSON.stringify(obj) === '{}';

// 方法4：Reflect.ownKeys
Reflect.ownKeys(obj).length === 0;
```

---

### 56. 浅拷贝和深拷贝的区别是什么？如何实现？


**回答：**

| 类型 | 说明 |
|------|------|
| 浅拷贝 | 只复制第一层，嵌套对象共享引用 |
| 深拷贝 | 递归复制所有层级，完全独立 |

**浅拷贝（展开运算符）**：
```javascript
const obj = { a: 1, b: { c: 2 } };
const copy = { ...obj };
copy.b.c = 99;
console.log(obj.b.c); // 99 ❗ 共享引用
```

**深拷贝（structuredClone）**：
前端开发中替代 JSON.parse(JSON.stringify()) 的更优方案
```javascript
const copy = structuredClone(obj);
copy.b.c = 99;
console.log(obj.b.c); // 2 ✅ 完全独立
```

---
