# JavaScript异步

## 【必考点 - 超高频】⭐⭐⭐

### 51. 异步编程的实现方式？

**回答：**

JavaScript 异步编程的主要实现方式：

1. **回调函数**
   ```javascript
   function fetchData(callback) {
     setTimeout(() => {
       callback('data');
     }, 1000);
   }
   fetchData(console.log);
   ```

2. **Promise**
   ```javascript
   const promise = new Promise((resolve, reject) => {
     setTimeout(() => resolve('data'), 1000);
   });
   promise.then(console.log);
   ```

3. **async/await**
   ```javascript
   async function fetchData() {
     const result = await new Promise(r => r('data'), 1000 => setTimeout(()));
     console.log(result);
   }
   ```

4. **事件监听**
   ```javascript
   element.addEventListener('click', handleClick);
   ```

5. **发布/订阅**
   ```javascript
   eventEmitter.on('event', handler);
   eventEmitter.emit('event');
   ```

---

### 52. setTimeout、Promise、Async/Await的区别

**回答：**

| 特性 | setTimeout | Promise | async/await |
|------|------------|---------|--------------|
| 本质 | 定时器回调 | 异步解决方案 | Promise 语法糖 |
| 语法 | 回调函数 | then/catch | 同步写法 |
| 错误处理 | 回调内处理 | catch | try/catch |
| 链式调用 | 嵌套回调 | 链式调用 | 顺序执行 |

### 示例对比

```javascript
// setTimeout - 回调地狱
setTimeout(() => {
  console.log(1);
  setTimeout(() => {
    console.log(2);
    setTimeout(() => {
      console.log(3);
    }, 1000);
  }, 1000);
}, 1000);

// Promise - 链式调用
new Promise(r => setTimeout(() => r(1), 1000))
  .then(v => { console.log(v); return 2; })
  .then(v => { console.log(v); return 3; })
  .then(v => console.log(v));

// async/await - 同步写法
async function fn() {
  console.log(await new Promise(r => setTimeout(() => r(1), 1000)));
  console.log(await new Promise(r => setTimeout(() => r(2), 1000)));
  console.log(await new Promise(r => setTimeout(() => r(3), 1000)));
}
```

---

### 53. Promise是什么？

**回答：**

Promise 是 ES6 提供的**异步编程解决方案**，代表一个异步操作的最终结果。

### 三种状态

| 状态 | 说明 |
|------|------|
| `pending` | 进行中 |
| `fulfilled` | 已成功 |
| `rejected` | 已失败 |

### 基本用法

```javascript
const promise = new Promise((resolve, reject) => {
  const success = true;
  if (success) {
    resolve('成功'); // 状态变为 fulfilled
  } else {
    reject('失败');  // 状态变为 rejected
  }
});

promise
  .then(result => console.log(result))  // 成功回调
  .catch(error => console.error(error)) // 失败回调
  .finally(() => console.log('完成'));  // 最终回调
```

### 特点

1. 状态一旦改变，不可逆
2. 支持链式调用
3. 可以解决回调地狱问题

---

### 54. Promise.all和Promise.race的区别？使用场景

**回答：**

### Promise.all()

- **作用**：等待**所有** Promise 都完成
- **返回值**：所有成功结果的数组
- **场景**：并发请求多个接口，等待全部返回

```javascript
const p1 = Promise.resolve(1);
const p2 = Promise.resolve(2);
const p3 = Promise.resolve(3);

Promise.all([p1, p2, p3])
  .then(values => console.log(values)); // [1, 2, 3]

// 一个失败
Promise.all([p1, Promise.reject('error'), p3])
  .catch(err => console.log(err)); // 'error'
```

### Promise.race()

- **作用**：返回**最先**完成（无论成功或失败）的结果
- **场景**：设置请求超时

```javascript
const request = fetch(url);
const timeout = new Promise((_, reject) => 
  setTimeout(() => reject('超时'), 5000)
);

Promise.race([request, timeout])
  .then(result => console.log(result))
  .catch(err => console.error(err));
```

### Promise.allSettled()

- **作用**：等待所有 Promise  settled（无论成功或失败）
- **场景**：不关心失败，只关心所有请求完成

```javascript
Promise.allSettled([p1, p2, p3])
  .then(results => {
    results.forEach(r => {
      if (r.status === 'fulfilled') console.log(r.value);
      else console.log(r.reason);
    });
  });
```

---

### 55. 对async/await的理解

**回答：**

async/await 是 ES8 提供的** Promise 语法糖**，让异步代码看起来像同步代码。

### 基本用法

```javascript
async function fn() {
  const result = await promise;
  console.log(result);
}
```

### 特点

1. `async` 函数总是返回 Promise
2. `await` 等待 Promise resolve
3. 代码更简洁、可读性更好

### 示例

```javascript
async function fetchUser() {
  try {
    const user = await fetch('/api/user').then(r => r.json());
    const posts = await fetch('/api/posts').then(r => r.json());
    return { user, posts };
  } catch (error) {
    console.error('请求失败:', error);
  }
}
```

---

### 56. async/await对比Promise的优势

**回答：**

| 方面 | Promise | async/await |
|------|---------|-------------|
| 可读性 | 链式调用，嵌套深 | 接近同步写法 |
| 错误处理 | catch 统一处理 | try/catch 更直观 |
| 调试 | 堆栈不连续 | 堆栈清晰 |
| 条件异步 | 逻辑复杂 | 清晰直观 |

### 代码对比

```javascript
// Promise
function getData() {
  return fetchAPI()
    .then(value1 => {
      if (value1) {
        return fetchMore()
          .then value1(value2 => ({, value2 }));
      }
    })
    .catch(err => console.error(err));
}

// async/await
async function getData() {
  try {
    const value1 = await fetchAPI();
    if (value1) {
      const value2 = await fetchMore();
      return { value1, value2 };
    }
  } catch (err) {
    console.error(err);
  }
}
```

### 优势总结

1. **代码更简洁**：避免链式调用的嵌套
2. **错误处理更直观**：使用熟悉的 try/catch
3. **调试更友好**：堆栈信息更清晰
4. **条件执行更清晰**：可以在 if/for 等语句中使用

---

### 57. async/await如何捕获异常

**回答：**

使用 `try/catch` 捕获异常：

```javascript
async function fn() {
  try {
    await somePromise();
  } catch (error) {
    console.error('捕获到错误:', error);
  }
}

// 多个 await
async function fn() {
  try {
    const a = await promise1();
    const b = await promise2();
    const c = await promise3();
  } catch (error) {
    // 任何一个失败都会到这里
    console.error('某个请求失败:', error);
  }
}

// 单独捕获
async function fn() {
  const result = await promise().catch(err => {
    console.error(err);
    return '默认值';
  });
}
```

---

### 58. 宏任务和微任务分别有哪些？

**回答：**

### 宏任务（Macro Task）

| 任务 | 说明 |
|------|------|
| `setTimeout` | 定时器 |
| `setInterval` | 间隔定时器 |
| `I/O` | 输入输出 |
| `UI rendering` | UI 渲染 |
| `requestAnimationFrame` | 动画帧 |

### 微任务（Micro Task）

| 任务 | 说明 |
|------|------|
| `Promise.then/catch/finally` | Promise 回调 |
| `MutationObserver` | Mutation 观察者 |
| `queueMicrotask` | 微任务队列 |
| `async/await` | await 后的代码 |

### 执行顺序

```
执行栈 → 微任务队列 → 宏任务队列 → 渲染 → 下一个宏任务
```

```javascript
console.log(1);

setTimeout(() => console.log(2), 0);

Promise.resolve().then(() => console.log(3));

console.log(4);

// 输出顺序: 1 → 4 → 3 → 2
```

---

### 59. 对事件循环的理解

**回答：**

事件循环（Event Loop）是 JavaScript 执行异步代码的机制。

### 执行流程

```
┌─────────────────────────┐
│        执行栈           │ ← 同步代码执行
│   (Call Stack)          │
└───────────┬─────────────┘
            │
            ▼
┌─────────────────────────┐
│       微任务队列        │ ← Promise、async
│   (Micro Task Queue)    │
└───────────┬─────────────┘
            │ 清空队列
            ▼
┌─────────────────────────┐
│       宏任务队列        │ ← setTimeout、I/O
│   (Macro Task Queue)    │
└───────────┬─────────────┘
            │ 取出一个执行
            │
            ▼
        渲染页面
            │
            ▼
      继续循环...
```

### 示例

```javascript
console.log('1'); // 同步

setTimeout(() => console.log('2'), 0); // 宏任务

Promise.resolve().then(() => console.log('3')); // 微任务

console.log('4'); // 同步

// 执行顺序: 1 → 4 → 3 → 2
```

### 关键点

1. 同步代码先执行
2. 微任务全部执行完再执行宏任务
3. 宏任务执行一个后，会检查并执行所有微任务

---

### 60. 什么是执行栈？

**回答：**

执行栈（Execution Stack）也称调用栈，用于存储代码执行时的**上下文**。

### 示例

```javascript
function a() {
  console.log('a start');
  b();
  console.log('a end');
}

function b() {
  console.log('b start');
  c();
  console.log('b end');
}

function c() {
  console.log('c');
}

a();

// 执行栈变化:
// [] → [a] → [a, b] → [a, b, c]
// 输出: a start → b start → c → b end → a end
```

### 特点

1. LIFO（后进先出）结构
2. 每次函数调用创建新的执行上下文
3. 栈溢出时抛出 `Maximum call stack size exceeded`

```javascript
// 递归调用导致栈溢出
function fn() {
  fn();
}
fn(); // RangeError: Maximum call stack size exceeded
```

## 【常考点 - 高频】⭐⭐

### 61. Promise解决了什么问题

**待回答**

---

### 62. 对Promise的理解

**待回答**

---

### 63. 并发与并行的区别？

**待回答**

---

### 64. 什么是回调函数？回调函数有什么缺点

**待回答**

---

### 65. 什么是AJAX？如何实现一个AJAX请求

**待回答**

---

### 66. Ajax和axios的区别

**待回答**

---

### 67. 常见的DOM操作有哪些

**待回答**

---

### 68. 什么是尾调用，使用尾调用有什么好处？

**待回答**

---

### 69. 强类型语言和弱类型语言的区别

**待回答**

---

### 70. 解释性语言和编译型语言的区别

**待回答**

---

## 【了解点 - 中低频】⭐

### 71. for...in和for...of的区别

**待回答**

---

### 72. 如何使用for...of遍历对象

**待回答**

---

### 73. 迭代遍历方法有哪些

**待回答**

---

### 74. 运行类型的检测方式有哪些

**待回答**

---

### 75. 什么是函数柯里化？

**待回答**

---
