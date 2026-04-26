# HTML/浏览器（30道）

## 4.1 渲染原理（10道）

### 136. 从URL输入到页面显示的过程是怎样的？

**回答：**

这是一个经典问题，主要分为以下阶段：
1. **DNS 解析**：将域名解析为 IP 地址（浏览器缓存 -> 系统缓存 -> 路由器缓存 -> ISP 缓存 -> 根域名服务器）。
2. **TCP 连接**：三次握手建立连接。
3. **发送 HTTP 请求**：浏览器发起请求。
4. **服务器处理请求并返回**：服务器返回 HTML、CSS、JS 等资源。
5. **浏览器渲染**：
   - 解析 HTML 生成 **DOM Tree**。
   - 解析 CSS 生成 **CSSOM Tree**。
   - 合并生成 **Render Tree**。
   - **Layout（布局）**：计算每个节点几何信息。
   - **Paint（绘制）**：绘制像素。
   - **Composite（合成）**：将各层合成显示在屏幕上。
6. **断开连接**：四次挥手断开 TCP 连接。

---

### 137. 什么是CRP（关键渲染路径）？

**回答：**

**关键渲染路径**是浏览器将 HTML、CSS 和 JavaScript 转换为屏幕像素所经历的一系列步骤。
优化 CRP 可以显著提升首屏渲染速度。核心关注点：
- 关键资源的数量。
- 关键路径的长度（往返次数）。
- 关键字节数。

---

### 138. 重绘和回流的区别是什么？

**回答：**

- **回流 (Reflow)**：当元素的尺寸、布局、隐藏等信息发生改变，导致浏览器需要重新计算几何属性并重新构建渲染树的过程。**开销很大**。
- **重绘 (Repaint)**：当元素的视觉外观发生改变（如 `color`, `background-color`），但不影响布局时，浏览器重新绘制元素。**开销较小**。
*注：回流必定触发重绘，重绘不一定触发回流。*

---

### 139. 如何避免回流？

**回答：**

1. **合并样式修改**：使用 `cssText` 或切换 `class` 一次性修改。
2. **脱离文档流**：对动画元素使用 `position: absolute/fixed`。
3. **离屏操作**：使用 `documentFragment` 或 `display: none` 修改完后再显示。
4. **避免频繁访问布局偏移属性**：如 `offsetTop`, `scrollTop`, `getComputedStyle()` 等，它们会强制触发浏览器同步刷新队列。
5. **使用 transform/opacity**：它们在合成层处理，不触发回流。

---

### 140. 什么是合成层？

**回答：**

浏览器渲染引擎将页面分为多个图层（Layers）。
- 拥有特定属性的元素（如 `transform: translateZ(0)`, `will-change`）会被提升为**独立合成层**。
- 合成层的位移、透明度变化是在 GPU 中完成的，**不会触发主线程的回流或重绘**，性能极高。

---

### 141. 渲染阻塞有哪些情况？

**回答：**

1. **HTML 解析阻塞**：遇到 `<script>` 标签时，解析会停止，直到脚本下载并执行完。
2. **CSS 阻塞**：CSS 不会阻塞 HTML 解析，但会**阻塞 Render Tree 构建**（即阻塞页面显示），且会**阻塞后面 JS 的执行**。
3. **JS 阻塞**：JS 的下载和执行都会阻塞 DOM 的构建。

---

### 142. DOM树和CSSOM树的构建过程？

**回答：**

- **DOM 树**：Bytes -> Characters -> Tokens -> Nodes -> DOM。
- **CSSOM 树**：Bytes -> Characters -> Tokens -> Nodes -> CSSOM。
两者都是**树形结构**，CSSOM 的构建也是增量且异步的，但必须构建完才能生成 Render Tree。

---

### 143. Render Tree是如何生成的？

**回答：**

1. 从 DOM 树根节点开始遍历。
2. 过滤掉不可见的节点（如 `display: none`, `<script>`, `<link>` 等）。
3. 为每个可见节点匹配 CSSOM 中的样式规则。
4. 生成带样式信息的 **Render Tree**。
*注：`visibility: hidden` 的节点仍在 Render Tree 中，因为它占据空间。*

---

### 144. Layout和Paint阶段做了什么？

**回答：**

- **Layout (布局)**：计算 Render Tree 中每个节点的精确位置和大小（几何信息）。
- **Paint (绘制)**：将 Layout 阶段计算出的信息转换为屏幕上的像素点，包括文本、颜色、图像、边框、阴影等。

---

### 145. Composite阶段做了什么？

**回答：**

**合成 (Compositing)** 阶段将页面中的各个图层按正确顺序合并，最终输出到屏幕。
- 它在合成线程中运行，利用 GPU 性能，避免占用主线程。

---

## 4.2 存储机制（6道）

### 146. cookie的属性有哪些？

**回答：**

- **Name/Value**：名称和值。
- **Domain**：生效域名。
- **Path**：生效路径。
- **Expires/Max-Age**：过期时间。
- **HttpOnly**：前端 JS 无法读取（防 XSS）。
- **Secure**：只在 HTTPS 协议下传输。
- **SameSite**：Strict/Lax/None（防 CSRF）。

---

### 147. localStorage和sessionStorage的区别？

**回答：**

- **localStorage**：永久存储，除非手动清理。跨窗口/标签页共享。
- **sessionStorage**：会话级存储，窗口关闭即消失。**不跨标签页共享**（即使是同源）。

---

### 148. IndexedDB的特点是什么？

**回答：**

- **非关系型数据库**（NoSQL）。
- **容量大**：通常不少于 250MB，甚至没有上限。
- **支持索引**：查询速度快。
- **异步操作**：不会阻塞主线程。
- **支持事务**：操作原子性。

---

### 149. Service Worker缓存的原理？

**回答：**

Service Worker 是运行在浏览器后台的独立线程。
- 它可以拦截网络请求。
- 配合 **Cache API**，可以实现离线缓存、精细化的缓存更新策略。
- 它是 PWA（渐进式 Web 应用）的核心。

---

### 150. 各种存储方式的容量限制？

**回答：**

| 方式 | 容量 |
|------|------|
| **Cookie** | 4KB 左右 |
| **LocalStorage** | 5MB 左右 |
| **SessionStorage** | 5MB 左右 |
| **IndexedDB** | 硬盘剩余空间的 50% 或更多 |

---

### 151. 如何选择合适的存储方案？

**回答：**

- **Cookie**：仅用于身份验证（SessionId/Token）。
- **LocalStorage**：存储持久化的非敏感业务数据。
- **SessionStorage**：存储仅在当前会话有效的临时数据（如表单填写一半的状态）。
- **IndexedDB**：存储大量结构化数据或需要离线运行的应用数据。

---

## 4.3 缓存策略（6道）

### 152. 强缓存和协商缓存的区别？

**回答：**

- **强缓存**：浏览器直接从本地缓存获取资源，**不向服务器发送请求**。状态码 200 (from memory/disk cache)。
- **协商缓存**：浏览器携带缓存标识（如 ETag）向服务器发起请求，由服务器决定是否使用缓存。如果资源未变，返回 **304 Not Modified**。

---

### 153. Cache-Control有哪些常用指令？

**回答：**

- **public**：响应可被任何中间代理（如 CDN）缓存。
- **private**：响应只能被浏览器缓存。
- **no-cache**：强制进行协商缓存（必须先问服务器）。
- **no-store**：禁止任何缓存。
- **max-age=xxx**：强缓存的有效时间（秒）。
- **s-maxage=xxx**：代理服务器（CDN）的缓存时间。

---

### 154. Expires和Cache-Control的区别？

**回答：**

- **Expires**：HTTP/1.0 产物，使用的是**绝对时间**（服务器时间）。如果客户端时间与服务器不一致，会导致缓存失效。
- **Cache-Control**：HTTP/1.1 产物，使用的是**相对时间**（max-age）。优先级高于 Expires。

---

### 155. Etag和Last-Modified的区别？

**回答：**

- **Last-Modified / If-Modified-Since**：基于**文件最后修改时间**。秒级单位，如果 1s 内修改多次，无法识别。
- **ETag / If-None-Match**：基于**文件内容生成的唯一标识（Hash）**。只要内容变了 ETag 就会变，更精确。优先级高于 Last-Modified。

---

### 156. 缓存位置有哪些？

**回答：**

按优先级排序：
1. **Service Worker**
2. **Memory Cache**（内存缓存：读取快，进程关闭消失）
3. **Disk Cache**（磁盘缓存：读取稍慢，持久存储）
4. **Push Cache**（HTTP/2 推送缓存）

---

### 157. 如何设计合理的缓存策略？

**回答：**

- **HTML**：`no-cache`（每次都协商，确保拿到最新版本）。
- **静态资源 (JS/CSS/图片)**：`max-age=31536000` (一年) + **文件名 Hash**（内容变了文件名就变，自动刷新）。
- **业务 API**：`no-store`（不缓存敏感数据）。

---

## 4.4 安全（5道）

### 158. XSS攻击的类型有哪些？如何防御？

**回答：**

**跨站脚本攻击 (Cross-Site Scripting)**
- **类型**：
  1. **存储型**：恶意脚本存入数据库（如评论区）。
  2. **反射型**：通过 URL 参数带入（如搜索框）。
  3. **DOM 型**：纯前端 JS 漏洞（如 `innerHTML`）。
- **防御**：
  - **输入过滤 / 输出转义**。
  - 使用 `HttpOnly` 防止 Cookie 被盗。
  - 开启 **CSP**（内容安全策略）。

---

### 159. CSRF攻击的原理是什么？如何防御？

**回答：**

**跨站请求伪造 (Cross-Site Request Forgery)**
- **原理**：攻击者诱导用户访问恶意网站，利用用户在目标网站的登录状态（Cookie），伪造用户的名义发送请求。
- **防御**：
  - **CSRF Token**：请求时携带一个随机 Token。
  - **Referer / Origin Check**：校验请求来源。
  - **SameSite Cookie**：设置 `SameSite=Lax/Strict`。

---

### 160. 什么是点击劫持？如何防御？

**回答：**

- **原理**：将目标网站通过 `iframe` 嵌入到恶意网页中，并设为透明，诱导用户点击。
- **防御**：
  - **X-Frame-Options**：设置 `DENY` 或 `SAMEORIGIN`。
  - **CSP**：`frame-ancestors 'self'`。

---

### 161. CSP策略的作用是什么？

**回答：**

**内容安全策略 (Content Security Policy)**
- 通过白名单机制，限制浏览器可以加载哪些外部资源（脚本、样式、图片等）。
- 即使发生 XSS，攻击者也无法从恶意域名加载脚本或发送数据。

---

### 162. HTTPS的工作原理是什么？

**回答：**

**HTTP + SSL/TLS**
1. **握手阶段**：
   - 客户端请求，服务端下发 **CA 证书**（含公钥）。
   - 客户端校验证书有效性。
   - 客户端生成 **对称密钥**，并用服务端公钥加密后传回。
   - 服务端用私钥解密，得到对称密钥。
2. **传输阶段**：
   - 双方使用 **对称密钥** 加密通信内容。
*核心：非对称加密交换密钥，对称加密传输数据。*

---

## 4.5 性能优化指标（3道）

### 163. FCP、LCP、CLS分别是什么？

**回答：**

- **FCP (First Contentful Paint)**：首次内容绘制。指浏览器首次绘制来自 DOM 的内容（文本、图像等）的时间。
- **LCP (Largest Contentful Paint)**：最大内容绘制。指可视区域内最大的图像或文本块完成渲染的时间。衡量**加载性能**（建议 < 2.5s）。
- **CLS (Cumulative Layout Shift)**：累积布局偏移。衡量**视觉稳定性**。值越小越好。

---

### 164. Performance API的用法？

**回答：**

```javascript
// 获取各项指标时间戳
const timing = performance.getEntriesByType('navigation')[0];
const dnsTime = timing.domainLookupEnd - timing.domainLookupStart;
const tcpTime = timing.connectEnd - timing.connectStart;

// 标记性能点
performance.mark('start-task');
// ... 执行任务
performance.mark('end-task');
performance.measure('task-duration', 'start-task', 'end-task');
```

---

### 165. 懒加载的实现方式？

**回答：**

1. **`<img>` 标签 loading 属性**：`<img src="..." loading="lazy">` (浏览器原生支持)。
2. **Intersection Observer API**：观察元素是否进入视口。
3. **监听 scroll 事件 + getBoundingClientRect**（传统方式，性能较差）。

---
