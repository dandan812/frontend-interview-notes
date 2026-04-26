# Vue专项（35道）

## 3.1 基础使用/选项式API（8道）

### 101. Vue local life cycle有哪些？各阶段做了什么？

**回答：**

| 阶段 | 钩子函数 | 描述 |
|------|----------|------|
| **创建** | `beforeCreate` | 实例初始化之后，数据观测和事件配置前（拿不到 `data`, `methods`） |
| | `created` | 实例创建完成，可操作 `data`, `methods`，但未挂载（常用于请求异步数据） |
| **挂载** | `beforeMount` | 模板编译完成，挂载前，虚拟 DOM 已生成 |
| | `mounted` | 挂载完成，可访问真实 DOM 节点 |
| **更新** | `beforeUpdate` | 数据更新，DOM 重新渲染前 |
| | `updated` | DOM 重新渲染并打补丁后 |
| **卸载** | `beforeUnmount` | 实例销毁前（Vue3 命名，Vue2 为 `beforeDestroy`） |
| | `unmounted` | 实例销毁后（Vue3 命名，Vue2 为 `destroyed`） |
| **其它** | `activated` | keep-alive 组件激活时 |
| | `deactivated` | keep-alive 组件停用时 |

---

### 102. created和mounted的区别是什么？

**回答：**

- **created**：实例在内存中创建完成，可以访问数据（data）和方法（methods），但此时 **DOM 尚未生成**，无法操作 DOM 元素。
- **mounted**：实例已经挂载到页面上，**真实 DOM 已生成**，可以进行 DOM 操作（如获取元素宽高、初始化三方库等）。

---

### 103. 一般在哪个生命周期请求异步数据？

**回答：**

通常在 **`created`** 或 **`mounted`**。
- **created**：最早能拿到数据的时机，减少白屏时间。
- **mounted**：如果需要根据异步数据操作 DOM，则必须在 mounted。
- **SSR（服务端渲染）**：由于服务端不支持 mounted，必须在 created 中执行。

---

### 104. computed和watch的区别是什么？

**回答：**

| 特性 | computed (计算属性) | watch (侦听器) |
|------|-------------------|---------------|
| **缓存** | ✅ 有缓存，依赖不变不重新计算 | ❌ 无缓存，变化即执行 |
| **异步** | ❌ 不支持异步，无法 return 异步结果 | ✅ 支持异步操作 |
| **目的** | 简化模板逻辑，派生新值 | 监听数据变化执行副作用（如发请求、操作 DOM） |
| **用法** | 当成属性用 | 监听具体数据 |

---

### 105. computed的缓存机制是怎样的？

**回答：**

1. `computed` 内部有一个 `dirty` 标志位。
2. 当依赖的数据发生变化时，`dirty` 设为 `true`。
3. 访问 `computed` 时，如果 `dirty` 为 `true`，则执行计算并更新缓存，然后将 `dirty` 设为 `false`。
4. 如果 `dirty` 为 `false`，则直接返回上一次计算的缓存值。

---

### 106. v-if和v-show的区别是什么？

**回答：**

- **v-if**：真正的条件渲染。它是**惰性**的，条件为假时不渲染 DOM。切换时会经历销毁和重建。适用于切换频率低、初始渲染开销大的场景。
- **v-show**：基于 CSS 的 `display: none`。无论条件真假都会渲染 DOM。适用于**频繁切换**的场景。

---

### 107. v-model的原理是什么？

**回答：**

`v-model` 是语法糖，本质是 **属性绑定 + 事件监听**。

- **Vue2 (input 元素)**：`:value` + `@input`。
- **Vue3 (默认)**：`:modelValue` + `@update:modelValue`。

```html
<!-- Vue3 展开写法 -->
<input :value="text" @input="text = $event.target.value">
<MyComponent :modelValue="val" @update:modelValue="val = $event" />
```

---

### 108. 组件通信的方式有哪些？

**回答：**

1. **父传子**：`props`。
2. **子传父**：`$emit` / `emits`。
3. **跨层级**：`provide / inject`。
4. **全局状态**：`Pinia` (Vue3) / `Vuex` (Vue2)。
5. **引用**：`ref` / `$refs` (父访问子)。
6. **插槽**：`slot` (分发内容)。
7. **事件总线**：`mitt` (Vue3) / `EventBus` (Vue2)。

---

## 3.2 组合式API（8道）

### 109. setup函数的执行时机是什么？

**回答：**

`setup` 在 **`beforeCreate` 之前**执行。此时组件实例尚未创建，因此 `setup` 内部**没有 `this`**，也无法访问 `data`, `computed`, `methods`。

---

### 110. ref和reactive的区别是什么？

**回答：**

| 特性 | ref | reactive |
|------|-----|----------|
| **数据类型** | 原始类型 + 对象 | 仅限**对象/数组** |
| **访问方式** | JS 中需 `.value`，模板中自动解包 | 直接访问 |
| **底层实现** | `Object.defineProperty` (value) | `Proxy` |
| **重新赋值** | 可重新赋值（响应式不丢失）| 重新赋值会**丢失响应式** |

---

### 111. toRef和toRefs的作用是什么？

**回答：**

- **toRef**：为 reactive 对象的某个属性创建 ref，保持响应式连接。
- **toRefs**：将 reactive 对象转为普通对象，但每个属性都是 ref。常用于 **解构 reactive 对象** 而不丢失响应式。

```javascript
const state = reactive({ count: 0 });
const { count } = toRefs(state); // count 现在是 ref
```

---

### 112. computed在组合式API中的用法？

**回答：**

```javascript
import { ref, computed } from 'vue';
const count = ref(0);
const double = computed(() => count.value * 2); // 只读
const plusOne = computed({
  get: () => count.value + 1,
  set: (val) => (count.value = val - 1)
}); // 可读写
```

---

### 113. watch和watchEffect的区别？

**回答：**

- **watch**：
  - 需要指定监听源。
  - 默认是惰性的（数据变化才执行）。
  - 可以获取旧值（oldValue）。
- **watchEffect**：
  - 自动追踪依赖（代码中用到谁就听谁）。
  - 立即执行一次（非惰性）。
  - 无法获取旧值。

---

### 114. 组合式API中的生命周期钩子有哪些？

**回答：**

相比选项式 API，命名加了 `on` 前缀：
- `onBeforeMount`
- `onMounted`
- `onBeforeUpdate`
- `onUpdated`
- `onBeforeUnmount`
- `onUnmounted`
- `onActivated`
- `onDeactivated`
*注意：没有 `onBeforeCreate` 和 `onCreated`，代码直接写在 `setup` 中即可。*

---

### 115. provide和inject的用法？

**回答：**

实现**跨层级组件通信**（依赖注入）。

```javascript
// 父组件
provide('theme', 'dark');

// 孙组件
const theme = inject('theme', 'default'); // 第二参数为默认值
```

---

### 116. 组合式函数（Composables）是什么？

**回答：**

利用组合式 API 封装的**有状态逻辑函数**。解决了 Vue2 Mixin 的缺点：
1. **命名冲突**：Composables 返回明确命名的变量。
2. **来源不明**：明确知道数据来自哪个 Composable。
3. **逻辑重用**：高度灵活，易于组合。

---

## 3.3 响应式原理（10道）

### 117. Vue2的响应式原理是什么？

**回答：**

核心是 **`Object.defineProperty`**。
1. 在初始化时，递归遍历 `data` 中的所有属性。
2. 使用 `Object.defineProperty` 将属性转为 **getter/setter**。
3. **Getter**：进行**依赖收集**（收集当前的 Watcher）。
4. **Setter**：**触发更新**（通知相关的 Watcher 重新渲染）。

---

### 118. Object.defineProperty()的缺陷有哪些？

**回答：**

1. **无法检测对象属性的添加或删除**（需使用 `Vue.set` / `Vue.delete`）。
2. **无法监控数组下标的变化和长度的变化**（Vue2 通过重写数组原型方法解决，但 `arr[0] = 1` 仍无效）。
3. **性能开销**：必须在初始化时递归遍历整个对象，如果对象很深，性能影响较大。

---

### 119. Vue3的响应式原理是什么？

**回答：**

核心是 **ES6 `Proxy`**。
1. 使用 `Proxy` 代理整个对象。
2. 在 `get` 拦截器中进行**依赖收集**（Track）。
3. 在 `set` 拦截器中**触发更新**（Trigger）。
4. 配合 `Reflect` 操作目标对象。

---

### 120. Proxy相比Object.defineProperty的优势？

**回答：**

1. **全方位监控**：可以检测到属性的新增、删除，以及数组下标的变化。
2. **性能更好**：不需要在初始化时递归遍历，只有在访问某个属性时才进行代理（惰性代理）。
3. **支持更多类型**：支持 Map、Set、WeakMap、WeakSet。

---

### 121. 什么是依赖收集？

**回答：**

在 getter 中，记录当前哪个组件（或 Effect）正在使用这个属性。
- Vue3 中使用 `targetMap` (WeakMap) -> `depsMap` (Map) -> `dep` (Set) 来存储这些依赖关系。

---

### 122. 什么是触发更新？

**回答：**

在 setter 中，找到之前收集的所有依赖（Effect），并依次重新执行它们，从而更新 DOM 或执行副作用。

---

### 123. Dep和Watcher的关系是什么？

**回答：**

这是 Vue2 的术语：
- **Dep**：Dependency，属性的依赖管理器。每个响应式属性都有一个 Dep。
- **Watcher**：订阅者。组件渲染、computed、watch 都会创建一个 Watcher。
- **关系**：多对多。一个 Dep 包含多个 Watcher（多个地方用了这个词），一个 Watcher 也可以观察多个 Dep（一个组件用了多个词）。

---

### 124. effect调度机制是怎样的？

**回答：**

Vue3 的副作用执行支持 **Scheduler（调度器）**。
- 默认情况下，副作用是同步触发的。
- 通过调度器，可以将更新放入微任务队列（异步更新），从而实现**批处理**，避免多次修改数据导致多次重复渲染。

---

### 125. Vue3的响应式系统有哪些优化？

**回答：**

1. **从 Object.defineProperty 转向 Proxy**（性能与功能双重提升）。
2. **静态依赖追踪**：减少不必要的依赖收集。
3. **响应式解耦**：响应式模块可以独立于 Vue 框架使用（@vue/reactivity）。
4. **惰性处理**：嵌套对象只有在被访问时才会被转为响应式。

---

### 126. shallowRef和shallowReactive的作用？

**回答：**

- **shallowRef**：只对 `.value` 这一层做响应式。如果 `.value` 是个对象，内部属性不是响应式的。
- **shallowReactive**：只对对象的第一层属性做响应式。
- **场景**：性能优化。当数据结构非常深且不需要深层响应式，或需要整合外部库时使用。

---

## 3.4 虚拟DOM/Diff（5道）

### 127. 什么是虚拟DOM？为什么要用虚拟DOM？

**回答：**

**虚拟 DOM** 是用普通的 JavaScript 对象来描述真实的 DOM 结构。
**原因**：
1. **跨平台**：一套代码可以渲染到 Web、Weex、小程序、Native 等。
2. **性能平衡**：虽然操作 JS 对象也耗时，但通过 Diff 算法找出最小变更，可以避免大规模的真实 DOM 昂贵操作。
3. **开发体验**：声明式编程，无需手动操作 DOM。

---

### 128. h函数的作用是什么？

**回答：**

`h` 是 **hyperscript** 的缩写，意为 "能生成 HTML 的 JavaScript"。
它用于**创建 VNode**（虚拟节点）。
```javascript
import { h } from 'vue';
h('div', { id: 'foo' }, 'hello'); // 创建 <div id="foo">hello</div> 的虚拟节点
```

---

### 129. patch的流程是怎样的？

**回答：**

1. **创建**：新节点有，旧节点无 -> 创建。
2. **删除**：旧节点有，新节点无 -> 删除。
3. **更新**：
   - 如果是相同类型节点（key 和 tag 相同）：对比属性、子节点（Diff）。
   - 如果是不同类型节点：直接删除旧的，挂载新的。

---

### 130. key的作用是什么？

**回答：**

`key` 是 VNode 的**唯一标识**。
- 在 Diff 算法中，通过 key 可以**快速匹配**新旧节点。
- 它可以**复用**现有的元素，避免不必要的销毁和重新创建，从而提升性能。
- *注意：不要使用 index 作为 key，因为删除/插入操作会导致 key 错位，引发错误的渲染。*

---

### 131. 双端比较算法的原理是什么？

**回答：**

这是 Vue2 使用的 Diff 算法：
同时维护新旧两组子节点的**四个指针**（新头、新尾、旧头、旧尾）。
1. 旧头 vs 新头
2. 旧尾 vs 新尾
3. 旧头 vs 新尾
4. 旧尾 vs 新头
通过这四种匹配方式，可以覆盖大部分移动、插入、删除场景，比单向遍历更高效。
*注：Vue3 使用了带有“最长递增子序列”算法的更高效 Diff 策略。*

---

## 3.5 编译优化（4道）

### 132. 什么是静态提升？

**回答：**

Vue 编译器会将那些**永远不会改变**的 VNode 节点提升到渲染函数之外。
- 这样在每次组件更新重新执行渲染函数时，这些静态节点会被**直接复用**，无需重新创建，节省内存和 GC 压力。

---

### 133. PatchFlag的作用是什么？

**回答：**

在编译阶段，给动态节点打上**补丁标记**（PatchFlag）。
- 标记记录了该节点哪些属性是动态的（如 TEXT, CLASS, PROPS）。
- 在运行时 Diff 阶段，Vue 只会对比这些带标记的属性，**跳过静态部分**，实现“靶向更新”。

---

### 134. Block Tree的作用是什么？

**回答：**

Vue3 将模板分为多个 **Block**（块）。
- 每个 Block 都会记录其内部所有的**动态子节点**（存入 `dynamicChildren` 数组）。
- 在更新时，只需遍历这个扁平的数组，而不需要递归遍历整个树，性能从“与模板大小成正比”变为“与动态内容数量成正比”。

---

### 135. 事件缓存机制是什么？

**回答：**

Vue 会将绑定的事件处理器（如 `@click`）**缓存**起来。
- 默认情况下，内联函数每次渲染都会被视为新函数，导致子组件更新。
- 开启缓存后，同一个位置的事件会复用缓存中的同一个函数，从而避免不必要的子组件重渲染。

---
