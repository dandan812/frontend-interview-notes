# CSS（45道）

## 2.1 Flex布局（12道）

### 56. Flex布局的基本概念是什么？

**回答：**

弹性盒子布局，一维布局，主轴+交叉轴。

```css
.container {
  display: flex;
}
```

---

### 57. flex-direction属性有哪些值？

**回答：**

| 值 | 方向 |
|------|------|
| row | 行（默认） |
| row-reverse | 反向行 |
| column | 列 |
| column-reverse | 反向列 |

---

### 58. justify-content属性有哪些值及作用？

**回答：**

主轴对齐方式：

| 值 | 作用 |
|------|------|
| flex-start | 起始对齐 |
| flex-end | 末尾对齐 |
| center | 居中 |
| space-between | 两端对齐 |
| space-around | 环绕 |
| space-evenly | 等距 |

---

### 59. align-items和align-content的区别？

**回答：**

| 属性 | 作用 | 场景 |
|------|------|------|
| align-items | 单行交叉轴对齐 | 单行 |
| align-content | 多行交叉轴对齐 | 多行（换行后）|

---

### 60. flex-wrap的作用是什么？

**回答：**

控制换行：

| 值 | 效果 |
|------|------|
| nowrap | 不换行（默认）|
| wrap | 换行 |
| wrap-reverse | 反向换行 |

---

### 61. flex-flow是什么的简写？

**回答：**

flex-direction + flex-wrap 的简写。

```css
flex-flow: row wrap; /* direction + wrap */
```

---

### 62. order属性的作用是什么？

**回答：**

控制项目排列顺序（数值越小越靠前）。

```css
.item:first-child { order: 2; }
.item:nth-child(2) { order: 1; }
```

---

### 63. flex-grow、flex-shrink、flex-basis的作用？

**回答：**

| 属性 | 作用 |
|------|------|
| flex-grow | 放大比例（默认0）|
| flex-shrink | 缩小比例（默认1）|
| flex-basis | 项目基准宽度 |

---

### 64. flex: 1表示什么？

**回答：**

`flex: 1` = `flex: 1 1 0%`

等分剩余空间。

```css
.item { flex: 1; } /* 均分容器 */
```

---

### 65. 如何实现圣杯布局？

**回答：**

左右固定，中间自适应 + header/footer。

```css
.container { display: flex; flex-direction: column; }
.main { flex: 1; }
.left, .right { width: 200px; }
```

---

### 66. 如何实现双飞翼布局？

**回答：**

圣杯变种，多一层 inner 包裹中间。

```css
.main-inner { margin: 0 200px; }
```

---

### 67. 如何实现底部固定布局？

**回答：**

```css
.container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
.main { flex: 1; }
```

---

## 2.2 Grid布局（8道）

### 68. Grid布局的基本概念是什么？

**回答：**

二维网格布局，行+列。

```css
.container {
  display: grid;
}
```

---

### 69. grid-template-columns/rows的用法？

**回答：**

定义行列尺寸。

```css
grid-template-columns: 100px 1fr 100px;
grid-template-rows: 50px 1fr;
```

---

### 70. repeat()和minmax()函数的用法？

**回答：**

```css
/* repeat(次数, 值) */
grid-template-columns: repeat(3, 1fr);

/* minmax(最小, 最大) */
grid-template-columns: repeat(3, minmax(100px, 1fr));
```

---

### 71. grid-gap/gap属性的作用？

**回答：**

行列间距。

```css
gap: 20px;       /* 行和列 */
row-gap: 10px;   /* 行间距 */
column-gap: 20px; /* 列间距 */
```

---

### 72. grid-area和grid-template-areas的用法？

**回答：**

```css
/* 定义区域 */
.container {
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
}

/* 放入区域 */
.item { grid-area: header; }
```

---

### 73. 如何实现自适应网格布局？

**回答：**

```css
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

---

### 74. Grid布局与Flex布局的区别？

**回答：**

| 维度 | Flex | Grid |
|------|------|------|
| 维度 | 一维 | 二维 |
| 方向 | 单一方向 | 行列同时 |
| 场景 | 导航、列表 | 整体页面 |

---

### 75. fr单位是什么？

**回答：**

比例单位，等分剩余空间。

```css
grid-template-columns: 1fr 2fr 1fr; /* 1:2:1 */
```

---

## 2.3 居中方案大全（8道）

### 76. 已知宽高的元素如何水平垂直居中？

**回答：**

```css
/* 定位 + margin */
.box {
  position: absolute;
  top: 50%; left: 50%;
  margin: -50px 0 0 -50px; /* 宽高一半 */
}
```

---

### 77. 未知宽高的元素如何水平垂直居中？

**回答：**

```css
/* 定位 + transform */
.box {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
}
```

---

### 78. 多行文本如何垂直居中？

**回答：**

```css
/* Flex */
.container { display: flex; align-items: center; }

/* Grid */
.container { display: grid; place-items: center; }

/* 行内 */
.container { line-height: 容器高度; }
```

---

### 79. 图片如何垂直居中？

**回答：**

```css
/* 定位 */
.img { position: absolute; top: 50%; transform: translateY(-50%); }

/* Flex */
.container { display: flex; align-items: center; }
```

---

### 80. 使用Flex实现居中的方法？

**回答：**

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

---

### 81. 使用Grid实现居中的方法？

**回答：**

```css
.container {
  display: grid;
  place-items: center;
}
```

---

### 82. 使用定位实现居中的方法？

**回答：**

```css
.box {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
}
```

---

### 83. 使用transform实现居中的方法？

**回答：**

```css
.box {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
}
```

---

## 2.4 层叠上下文/BFC（7道）

### 84. 什么是层叠上下文？如何创建？

**回答：**

元素在 Z 轴上的层叠顺序。

**创建条件**：
- 根元素
- position + z-index
- flex item + z-index
- opacity < 1
- transform != none
- filter != none

---

### 85. z-index失效的场景有哪些？

**回答：**

- 父元素 z-index 较小
- position: static
- 浮动元素

---

### 86. 什么是BFC（块级格式化上下文）？

**回答：**

独立的渲染区域，内部布局不影响外部。

---

### 87. 如何创建BFC？

**回答：**

- float != none
- position != static/relative
- display: inline-block/table/flex/grid
- overflow != visible

---

### 88. BFC的应用场景有哪些？

**回答：**

- 清除浮动
- 防止 margin 塌陷
- 自适应两栏布局

---

### 89. 如何清除浮动？

**回答：**

```css
/* 父元素 overflow */
.parent { overflow: hidden; }

/* 伪元素 */
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}

/* Grid/Flex */
.parent { display: flow-root; }
```

---

### 90. 什么是margin塌陷？如何解决？

**回答：**

上下 margin 重叠为最大值。

**解决**：
- 改为 padding
- 创建 BFC
- 父元素 overflow: hidden

---

## 2.5 响应式/移动端适配（6道）

### 91. rem适配方案的原理是什么？

**回答：**

根据根元素字体大小动态计算。

```css
html { font-size: 16px; }
.box { width: 2rem; } /* 32px */
```

---

### 92. vw/vh适配方案的原理？

**回答：**

视口单位。

```css
.box { width: 50vw; height: 50vh; }
```

---

### 93. flexible.js的原理是什么？

**回答：**

动态设置 html font-size = 屏幕宽度 / 10

```javascript
const dpr = window.devicePixelRatio;
const scale = 1 / dpr;
metaEl.setAttribute('data-scale', scale);
```

---

### 94. 如何实现1px边框？

**回答：**

```css
/* transform */
.border { transform: scaleY(0.5); }

/* 伪元素 */
.border::after {
  content: '';
  transform: scaleY(0.5);
}

/* viewport */
<meta name="viewport" content="width=device-width, initial-scale=0.5">
```

---

### 95. 什么是安全区适配？

**回答：**

适配刘海屏/圆角屏。

```css
padding-bottom: env(safe-area-inset-bottom);
padding-left: env(safe-area-inset-left);
```

---

### 96. 媒体查询的常用断点有哪些？

**回答：**

```css
/* 移动端 */
@media (max-width: 768px) {}

/* 平板 */
@media (min-width: 769px) and (max-width: 1024px) {}

/* 桌面 */
@media (min-width: 1025px) {}
```

---

## 2.6 动画/过渡/性能（4道）

### 97. transition和animation的区别？

**回答：**

| 特性 | transition | animation |
|------|------------|-----------|
| 触发 | 状态变化 | 自动/循环 |
| 关键帧 | 无 | 有 |
| 控制 | 有限 | 精细 |

```css
/* transition */
.box { transition: all 0.3s; }

/* animation */
@keyframes fade { from { opacity: 0; } }
.box { animation: fade 1s; }
```

---

### 98. GPU加速的原理是什么？

**回答：**

使用 GPU 渲染，不触发重绘/回流。

```css
/* 触发 GPU */
transform: translateZ(0);
transform: translate3d(0,0,0);
will-change: transform;
```

---

### 99. will-change属性的作用？

**回答：**

告知浏览器提前优化。

```css
.box { will-change: transform, opacity; }
```

---

### 100. 如何排查动画卡顿问题？

**回答：**

- 使用 Chrome DevTools Performance
- 避免触发重排的属性（width/height/top/left）
- 使用 transform/opacity
- 减少 DOM 操作

