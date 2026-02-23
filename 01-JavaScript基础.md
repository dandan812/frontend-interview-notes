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

**待回答**

---

### 6. 执行上下文的生命周期是怎样的？

**待回答**

---

### 7. 什么是闭包？闭包的形成条件是什么？

**待回答**

---

### 8. 闭包有哪些应用场景？

**待回答**

---

### 9. 闭包会导致内存泄漏吗？如何避免？

**待回答**

---

### 10. 什么是立即执行函数（IIFE）？有什么作用？

**待回答**

---

### 11. IIFE与块级作用域的关系是什么？

**待回答**

---

### 12. 如何理解"函数是一等公民"？

**待回答**

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
