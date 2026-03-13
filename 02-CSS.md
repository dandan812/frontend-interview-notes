# CSS（45道）

## 2.1 Flex布局（12道）

### 56. Flex布局的基本概念是什么？

**回答：**

弹性盒子布局，一维布局模型。

- **容器**：display: flex
- **项目**：容器的直接子元素
- **轴**：主轴（默认横向）+ 交叉轴（垂直于主轴）

```css
.container {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
}
```

---

### 57. flex-direction属性有哪些值？

**回答：**

| 值 | 方向 | 轴 |
|------|------|-----|
| row | 行（默认） | 左→右 |
| row-reverse | 反向行 | 右→左 |
| column | 列 | 上→下 |
| column-reverse | 列 | 下→上 |

---

### 58. justify-content属性有哪些值及作用？

**回答：**

主轴对齐方式：

| 值 | 作用 | 图示 |
|------|------|-----|
| flex-start | 起始对齐 | [1][2][3]___ |
| flex-end | 末尾对齐 | ___[1][2][3] |
| center | 居中 | ___[1][2][3]___ |
| space-between | 两端对齐 | [1]___[2]___[3] |
| space-around | 环绕 | _[1]__[2]__[3]_ |
| space-evenly | 等距 | _[1]__[2]__[3]_ |

---

### 59. align-items和align-content的区别？

**回答：**

| 属性 | 作用 | 适用场景 |
|------|------|----------|
| align-items | 单行交叉轴对齐 | 单行/nowrap |
| align-content | 多行整体对齐 | 多行/wrap |

```css
/* 单行 - align-items */
align-items: flex-start | center | flex-end | stretch | baseline

/* 多行 - align-content */
align-content: flex-start | center | flex-end | stretch | space-between | space-around
```

---

### 60. flex-wrap的作用是什么？

**回答：**

控制项目是否换行：

| 值 | 效果 |
|------|------|
| nowrap | 不换行（默认），会压缩项目 |
| wrap | 换行 |
| wrap-reverse | 反向换行（第一行在底部）|

---

### 61. flex-flow是什么的简写？

**回答：**

flex-direction + flex-wrap 的简写属性。

```css
/* 完整写法 */
flex-direction: row;
flex-wrap: wrap;

/* 简写 */
flex-flow: row wrap;
```

---

### 62. order属性的作用是什么？

**回答：**

控制项目在主轴上的排列顺序（数值越小越靠前，默认为0）。

```css
.item-1 { order: 3; }
.item-2 { order: 1; }
.item-3 { order: 2; }
// 实际顺序：item-2 → item-3 → item-1
```

---

### 63. flex-grow、flex-shrink、flex-basis的作用？

**回答：**

| 属性 | 作用 | 默认值 |
|------|------|--------|
| flex-grow | 放大比例（剩余空间）| 0 |
| flex-shrink | 缩小比例（空间不足）| 1 |
| flex-basis | 项目基准宽度 | auto |

```css
/* 基准宽度 */
flex-basis: 100px; /* 相当于 width: 100px */

/* 放大 */
flex-grow: 1; /* 等分剩余空间 */

/* 缩小 */
flex-shrink: 0; /* 不缩小 */
```

---

### 64. flex: 1表示什么？

**回答：**

`flex: 1` 是 `flex: 1 1 0%` 的简写。

| 值 | 含义 |
|------|------|
| 1 | flex-grow: 1（等分剩余空间）|
| 1 | flex-shrink: 1（空间不足时缩小）|
| 0% | flex-basis: 0%（不考虑原始宽度）|

```css
.container { width: 600px; }
.item { flex: 1; }
/* 每个 item 占 200px */
```

---

### 65. 如何实现圣杯布局？

**回答：**

左右固定宽度，中间自适应 + header/footer。

```css
.container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
.header, .footer { height: 60px; }
.main {
  flex: 1;
  display: flex;
}
.left, .right { width: 200px; }
.content { flex: 1; }

/* 调换顺序：中间放最前 */
.content { order: -1; }
```

---

### 66. 如何实现双飞翼布局？

**回答：**

圣杯布局变种，中间多一层 inner 包裹。

```css
.main {
  float: left;
  width: 100%;
}
.main-inner {
  margin: 0 200px; /* 左右留空 */
}
.left, .right {
  float: left;
  width: 200px;
}
.left { margin-left: -100%; }
.right { margin-left: -200px; }
```

---

### 67. 如何实现底部固定布局？

**回答：**

```css
/* Flex 方案（推荐）*/
.container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
.main { flex: 1; }

/* Grid 方案 */
.container {
  display: grid;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}
```

---

## 2.2 Grid布局（8道）

### 68. Grid布局的基本概念是什么？

**回答：**

二维网格布局系统，行+列同时控制。

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: 100px 1fr;
}
```

---

### 69. grid-template-columns/rows的用法？

**回答：**

定义网格轨道尺寸：

```css
/* 固定宽度 */
grid-template-columns: 100px 200px 100px;

/* 比例单位 */
grid-template-columns: 1fr 2fr 1fr;

/* 混合 */
grid-template-columns: 100px 1fr 100px;

/* 命名行 */
grid-template-columns: [start] 1fr [center] 1fr [end];
```

---

### 70. repeat()和minmax()函数的用法？

**回答：**

```css
/* repeat(次数, 值) - 重复轨道 */
grid-template-columns: repeat(3, 1fr);
grid-template-columns: repeat(auto-fill, 200px);

/* minmax(最小, 最大) - 范围 */
grid-template-columns: repeat(3, minmax(100px, 1fr));

/* 组合使用 */
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

---

### 71. grid-gap/gap属性的作用？

**回答：**

设置网格间距：

```css
/* 简写 */
gap: 20px;

/* 分别设置 */
row-gap: 10px;    /* 行间距 */
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

/* 分配项目 */
.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
.footer { grid-area: footer; }
```

---

### 73. 如何实现自适应网格布局？

**回答：**

```css
/* auto-fit + minmax 自动填充 */
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));

/* auto-fill 类似，但保留空白轨道 */
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
```

---

### 74. Grid布局与Flex布局的区别？

**回答：**

| 维度 | Flex | Grid |
|------|------|------|
| 维度 | 一维 | 二维 |
| 方向 | 单一方向 | 行列同时 |
| 粒度 | 项目级别 | 轨道级别 |
| 场景 | 导航、表单、列表 | 整体页面、相册、仪表盘 |
| 对齐 | justify/align | place-items/place-self |

---

### 75. fr单位是什么？

**回答：**

比例单位，表示可用空间的等分。

```css
/* 1:2:1 比例 */
grid-template-columns: 1fr 2fr 1fr;

/* 结合固定值 */
grid-template-columns: 200px 1fr 200px;

/* 等同于 */
grid-template-columns: repeat(3, 1fr);
```

---

## 2.3 居中方案大全（8道）

### 76. 已知宽高的元素如何水平垂直居中？

**回答：**

```css
/* 方案1：定位 + margin */
.box {
  position: absolute;
  top: 50%; left: 50%;
  width: 200px; height: 200px;
  margin: -100px 0 0 -100px;
}

/* 方案2：定位 + calc */
.box {
  position: absolute;
  top: calc(50% - 100px);
  left: calc(50% - 100px);
}
```

---

### 77. 未知宽高的元素如何水平垂直居中？

**回答：**

```css
/* 方案1：定位 + transform */
.box {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
}

/* 方案2：margin: auto */
.box {
  position: absolute;
  top: 0; bottom: 0;
  left: 0; right: 0;
  margin: auto;
}
```

---

### 78. 多行文本如何垂直居中？

**回答：**

```css
/* Flex（推荐）*/
.container {
  display: flex;
  align-items: center;
}

/* Grid */
.container {
  display: grid;
  place-items: center;
}

/* 行内元素 */
.container {
  line-height: 容器高度;
}

/* 多行文字垂直居中（table-cell）*/
.container {
  display: table-cell;
  vertical-align: middle;
}
```

---

### 79. 图片如何垂直居中？

**回答：**

```css
/* 定位 + transform */
.img-wrapper {
  position: relative;
}
.img {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}

/* Flex */
.img-wrapper {
  display: flex;
  align-items: center;
}

/* Grid */
.img-wrapper {
  display: grid;
  place-items: center;
}
```

---

### 80. 使用Flex实现居中的方法？

**回答：**

```css
.container {
  display: flex;
  justify-content: center;  /* 水平 */
  align-items: center;      /* 垂直 */
}

/* 简写 */
.container {
  display: flex;
  place-items: center;
}
```

---

### 81. 使用Grid实现居中的方法？

**回答：**

```css
/* place-items 简写 */
.container {
  display: grid;
  place-items: center;
}

/* 分别设置 */
.container {
  display: grid;
  justify-items: center;
  align-items: center;
}
```

---

### 82. 使用定位实现居中的方法？

**回答：**

```css
/* 未知宽高 */
.box {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
}

/* 已知宽高 */
.box {
  position: absolute;
  top: 50%; left: 50%;
  margin-left: -width/2;
  margin-top: -height/2;
}
```

---

### 83. 使用transform实现居中的方法？

**回答：**

```css
/* 必须配合定位 */
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

元素在 Z 轴上的层叠顺序，层叠上下文可以嵌套。

**创建条件**：
- 根元素 (html)
- position: absolute/relative + z-index: auto 以外的值
- position: fixed/sticky
- flex item + z-index: auto 以外的值
- grid item + z-index: auto 以外的值
- opacity < 1
- transform != none
- filter != none
- -webkit-overflow-scrolling: touch

---

### 85. z-index失效的场景有哪些？

**回答：**

- 父元素 z-index 较小，子元素 z-index 再大也没用
- position: static（z-index 只对定位元素有效）
- 浮动元素
- 元素未创建层叠上下文

---

### 86. 什么是BFC（块级格式化上下文）？

**回答：**

独立的渲染区域，内部布局不影响外部。

**特点**：
- 垂直方向排列
- margin 不会和子元素重叠
- 不会影响浮动元素

---

### 87. 如何创建BFC？

**回答：**

```css
/* 任选其一 */
float: left/right;
position: absolute/fixed;
display: inline-block/table/table-cell/flex/grid;
overflow: hidden/auto/scroll;
display: flow-root; /* 纯 BFC，无副作用 */
```

---

### 88. BFC的应用场景有哪些？

**回答：**

- 清除浮动（父元素创建 BFC）
- 防止 margin 塌陷（兄弟元素或父子）
- 自适应两栏布局

```css
/* 两栏布局 */
.left { float: left; width: 200px; }
.right { overflow: hidden; } /* 创建 BFC */
```

---

### 89. 如何清除浮动？

**回答：**

```css
/* 方案1：父元素 overflow */
.parent { overflow: hidden; }

/* 方案2：伪元素（常用）*/
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}

/* 方案3：display: flow-root */
.parent { display: flow-root; }

/* 方案4：空标签 */
<div style="clear: both;"></div>
```

---

### 90. 什么是margin塌陷？如何解决？

**回答：**

上下 margin 重叠为较大值。

**解决**：

```css
/* 1. 改为 padding */
margin-top: 20px; /* 改 */
padding-top: 20px;

/* 2. 创建 BFC */
.parent { overflow: hidden; }

/* 3. 浮动/定位 */
.parent { float: left; }
.parent { position: absolute; }

/* 4. Flex/Grid */
.parent { display: flex; }
```

---

## 2.5 响应式/移动端适配（6道）

### 91. rem适配方案的原理是什么？

**回答：**

根据根元素 font-size 动态计算。

```css
/* 16px 根字体 */
html { font-size: 16px; }
.box { width: 2rem; } /* 32px */

/* 动态计算：屏幕宽度 / 10 */
@media screen and (min-width: 320px) { html { font-size: 32px; } }
@media screen and (min-width: 375px) { html { font-size: 37.5px; } }
@media screen and (min-width: 414px) { html { font-size: 41.4px; } }
```

---

### 92. vw/vh适配方案的原理？

**回答：**

视口单位，1vw = 1% 视口宽度。

```css
/* 100vw = 满屏宽度 */
.box { width: 50vw; height: 50vh; }

/* 配合 rem */
html { font-size: 16px; }
.box { width: calc(100vw / 10); } /* 1rem = 10vw */
```

---

### 93. flexible.js的原理是什么？

**回答：**

淘宝开源的适配方案，核心：

```javascript
// 动态设置 meta
var metaEl = document.createElement('meta');
metaEl.setAttribute('name', 'viewport');
// 1 设计稿宽度 / 10 = 1rem 对应的 px 值
var scale = 1 / (dpr || 1);
metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
document.documentElement.style.fontSize = deviceWidth / 10 + 'px';
```

---

### 94. 如何实现1px边框？

**回答：**

```css
/* 方案1：transform 缩放 */
.border { transform: scaleY(0.5); transform-origin: top; }

/* 方案2：伪元素 */
.border::after {
  content: '';
  position: absolute;
  left: 0; top: 0;
  width: 100%; height: 1px;
  transform: scaleY(0.5);
}

/* 方案3：viewport 缩放 */
<meta name="viewport" content="width=device-width, initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no">

/* 方案4：border-image */
border: 1px solid #ccc;
border-image: url() 1 stretch;
```

---

### 95. 什么是安全区适配？

**回答：**

适配刘海屏、圆角屏、安全区域。

```css
/* CSS 环境变量 */
padding-bottom: env(safe-area-inset-bottom);
padding-top: env(safe-area-inset-top);
padding-left: env(safe-area-inset-left);
padding-right: env(safe-area-inset-right);

/* JS 获取 */
safeAreaInsets = window.navigator.safeAreaInsets
```

---

### 96. 媒体查询的常用断点有哪些？

**回答：**

```css
/* 移动设备 */
@media only screen and (max-width: 320px) { /* iPhone 4 */ }
@media only screen and (max-width: 375px) { /* iPhone 6/7/8 */ }
@media only screen and (max-width: 414px) { /* iPhone 6/7/8 Plus */ }

/* 平板 */
@media only screen and (min-width: 768px) and (max-width: 1024px) { }

/* 桌面 */
@media only screen and (min-width: 1025px) { }

/* 常用断点 */
@media (max-width: 768px)  { /* 手机 */ }
@media (min-width: 769px) and (max-width: 1024px) { /* 平板 */ }
@media (min-width: 1025px) { /* 桌面 */ }
```

---

## 2.6 动画/过渡/性能（4道）

### 97. transition和animation的区别？

**回答：**

| 特性 | transition | animation |
|------|------------|-----------|
| 触发 | 状态变化 (:hover, JS) | 自动/无限循环 |
| 关键帧 | 起点→终点 | 多关键帧 |
| 控制 | 简单（direction, duration）| 精细（name, duration, delay, iteration-count, timing-function）|
| 性能 | 较好 | 较好 |

```css
/* transition - 状态变化 */
.box {
  transition: all 0.3s ease;
}
.box:hover { transform: scale(1.1); }

/* animation - 关键帧 */
@keyframes fade {
  0% { opacity: 0; }
  50% { opacity: 0.5; }
  100% { opacity: 1; }
}
.box { animation: fade 1s ease-in-out infinite; }
```

---

### 98. GPU加速的原理是什么？

**回答：**

使用 GPU 渲染，避过 CPU 布局/绘制。

**触发 GPU 的属性**：
- transform: translateZ(0)
- transform: translate3d(0,0,0)
- will-change: transform
- opacity < 1 且 > 0
- filter

```css
/* 开启 GPU 加速 */
.box {
  transform: translate3d(0, 0, 0);
  /* 或 */
  will-change: transform;
}
```

---

### 99. will-change属性的作用？

**回答：**

告知浏览器提前优化，**慎用**。

```css
/* 提前告知 */
.box { will-change: transform, opacity; }

/* 使用后移除 */
.box { will-change: auto; }
```

**注意**：不要滥用，会增加内存消耗。

---

### 100. 如何排查动画卡顿问题？

**回答：**

1. **Chrome DevTools**
   - Performance 面板录制
   - 查看 FPS
   - 红色区域 = 掉帧

2. **优化建议**
   - 避免触发重排的属性：width/height/top/left/margin/padding
   - 使用 transform/opacity
   - 使用 GPU 加速
   - 减少 DOM 操作
   - 使用 will-change 提前优化
   - 动画元素脱离文档流（position: absolute）

3. **CSS 属性性能排名**
   - 最优：transform, opacity
   - 较好：filter, perspective
   - 较差：width, height, top, left
