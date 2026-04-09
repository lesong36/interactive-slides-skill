---
name: interactive-slides
description: "生成交互式 HTML 幻灯片演示文稿，适合用于教学、学术讲解、课程内容展示。当用户提供学习材料、笔记、课程内容、知识点，并要求生成幻灯片、演示文稿、slides、交互式讲义时，必须使用此技能。触发关键词包括：幻灯片、slides、演示文稿、交互式幻灯片、课件、教学课件、interactive slides、slideshow。即使用户只说「帮我做成幻灯片」或「把这个内容做成slides」也应触发此技能。"
---

# Interactive Slides Skill

当用户提供上下文内容，生成一套可在浏览器中交互浏览的 HTML 幻灯片。输出为单文件 HTML，双击即可在浏览器中打开，无需服务器。

---

## 一、内容规划原则

### 幻灯片结构

每个核心概念独立一幕，按以下顺序组织：

1. **概念引入** — 一句话定义 + 中英文术语对照
2. **直觉建立** — 生活类比（必须具体，不可用「类似于」这种模糊表述）
3. **结构图示** — 流程图、关系图、步骤图（见「图示规范」）
4. **公式/推导** — 若有数学内容，用 MathJax 渲染
5. **例子/代码** — 具体数值例子或代码示例（单独一幕）

### 信息密度平衡

目标：**每张幻灯片让读者在 30 秒内读完，但读完后有真实收获。**

- 定义：2-4 句，讲清楚「是什么」和「为什么重要」
- 类比：1-3 句，必须落到具体场景
- 图示：用视觉代替文字，不要既有图又有大段文字描述图
- 公式推导：每步单独一幕，每步有一句话解释「这步在做什么」
- 禁止：纯文字列表超过 5 条、段落超过 5 行

---

## 二、页内交互规范（核心要求）

**「交互式」不只是翻页。** 每张幻灯片至少使用以下一种页内交互模式：

### 模式 A：步骤展开（Step Reveal）

适用于：推导过程、算法步骤、逻辑链

```html
<!-- 初始只显示第一步，点击「下一步」逐步展开 -->
<div id="steps">
  <div class="step active">步骤 1 内容</div>
  <div class="step hidden">步骤 2 内容</div>
  <div class="step hidden">步骤 3 内容</div>
</div>
<button onclick="revealNext()">下一步 →</button>

<script>
let stepIdx = 0;
function revealNext() {
  const steps = document.querySelectorAll('#steps .step');
  stepIdx = Math.min(stepIdx + 1, steps.length - 1);
  steps[stepIdx].classList.remove('hidden');
  steps[stepIdx].style.animation = 'fadeIn 0.4s ease';
  if (stepIdx === steps.length - 1) document.querySelector('button').disabled = true;
}
</script>
```

### 模式 B：Tab 切换（Definition / Example / Formula）

适用于：概念讲解页，让读者按需深入

```html
<div class="tabs">
  <button class="tab active" onclick="switchTab('def')">📖 定义</button>
  <button class="tab" onclick="switchTab('eg')">💡 例子</button>
  <button class="tab" onclick="switchTab('formula')">📐 公式</button>
</div>
<div id="tab-def" class="tab-panel">定义内容</div>
<div id="tab-eg" class="tab-panel hidden">例子内容</div>
<div id="tab-formula" class="tab-panel hidden">公式内容</div>

<script>
function switchTab(name) {
  document.querySelectorAll('.tab-panel').forEach(p => p.classList.add('hidden'));
  document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
  document.getElementById('tab-' + name).classList.remove('hidden');
  event.target.classList.add('active');
}
</script>
```

### 模式 C：点击揭示（Click to Reveal）

适用于：「你认为答案是什么？」→ 点击揭示

```html
<div style="cursor:pointer; background:#f5f5f5; border-radius:10px; padding:16px;"
     onclick="this.innerHTML='<strong>真实答案：...</strong>'; this.style.background='#eafaf1'">
  🤔 点击揭示答案
</div>
```

### 模式 D：可展开详情（Expandable Detail）

适用于：核心内容 + 可选深度内容

```html
<div class="card">
  <div>核心内容（始终可见）</div>
  <div id="detail" style="display:none; margin-top:12px; color:#555;">
    深度解释（点击展开）
  </div>
  <button onclick="toggle('detail')" style="margin-top:8px; font-size:0.95em;">
    ▶ 查看详细解释
  </button>
</div>
<script>
function toggle(id) {
  const el = document.getElementById(id);
  const btn = el.nextElementSibling;
  if (el.style.display === 'none') {
    el.style.display = 'block'; btn.textContent = '▲ 收起';
  } else {
    el.style.display = 'none'; btn.textContent = '▶ 查看详细解释';
  }
}
</script>
```

### 模式 E：公式高亮标注（Formula Tooltip）

适用于：有多个变量的公式，鼠标悬浮查看含义

```html
<div style="text-align:center; font-size:1.3em; padding:20px;">
  \[ P(H \mid E) = \frac{
    \htmlClass{hl-likelihood}{P(E \mid H)} \cdot \htmlClass{hl-prior}{P(H)}
  }{\htmlClass{hl-evidence}{P(E)}} \]
</div>
<!-- 图例说明 -->
<div style="display:flex; gap:16px; justify-content:center; flex-wrap:wrap; margin-top:12px;">
  <span style="background:#ebf5fb; padding:4px 12px; border-radius:20px; font-size:1em;">
    <strong style="color:#1a5276;">P(E|H)</strong> 似然
  </span>
  <span style="background:#fef9e7; padding:4px 12px; border-radius:20px; font-size:1em;">
    <strong style="color:#9a7b0a;">P(H)</strong> 先验
  </span>
  <span style="background:#eafaf1; padding:4px 12px; border-radius:20px; font-size:1em;">
    <strong style="color:#1e8449;">P(E)</strong> 归一化项
  </span>
</div>
```

---

## 三、图示规范（必须使用，不可只用文字）

### 何时必须用图

- 流程/步骤关系 → **Mermaid flowchart**
- 因果/依赖关系 → **Mermaid graph**
- 对比/分类 → **HTML CSS 网格对比表**
- 数据分布/比例 → **HTML/CSS 可视化条形图**
- 层级嵌套结构 → **HTML 缩进卡片树**

### Mermaid 流程图（必须引入）

```html
<head>
  <script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
</head>
```

在 `showSlide()` 末尾重新初始化：
```js
const mEls = container.querySelectorAll('.mermaid');
if (mEls.length) {
  // 清除已渲染标记，强制重新渲染
  mEls.forEach(el => { el.removeAttribute('data-processed'); });
  mermaid.init(undefined, mEls);
}
```

用法示例（因果图）：
```html
<div class="mermaid">
graph LR
  A[原因 X] -->|影响| B[结果 Y]
  C[混淆变量] --> A
  C --> B
  style C fill:#fef9e7,stroke:#9a7b0a
</div>
```

### HTML/CSS 步骤流程图

不依赖 Mermaid，直接用 HTML 绘制，适合简单线性流程：

```html
<div style="display:flex; align-items:center; gap:8px; flex-wrap:wrap; justify-content:center;">
  <div style="background:#ebf5fb; border:2px solid #1a5276; border-radius:10px;
              padding:10px 18px; font-size:1.05em; color:#1a5276; font-weight:600;">
    步骤 1
  </div>
  <div style="font-size:1.4em; color:#aaa;">→</div>
  <div style="background:#fef9e7; border:2px solid #9a7b0a; border-radius:10px;
              padding:10px 18px; font-size:1.05em; color:#9a7b0a; font-weight:600;">
    步骤 2
  </div>
  <div style="font-size:1.4em; color:#aaa;">→</div>
  <div style="background:#eafaf1; border:2px solid #1e8449; border-radius:10px;
              padding:10px 18px; font-size:1.05em; color:#1e8449; font-weight:600;">
    步骤 3
  </div>
</div>
```

### HTML/CSS 对比表（优于纯 Markdown 表格）

```html
<div style="display:grid; grid-template-columns:1fr 1fr; gap:16px;">
  <div style="background:#fdedec; border-radius:12px; padding:18px;">
    <div style="font-weight:700; color:#c0392b; margin-bottom:8px;">❌ 相关关系</div>
    <div style="font-size:1.1em; line-height:1.75; color:#2c3e50;">内容...</div>
  </div>
  <div style="background:#eafaf1; border-radius:12px; padding:18px;">
    <div style="font-weight:700; color:#1e8449; margin-bottom:8px;">✅ 因果关系</div>
    <div style="font-size:1.1em; line-height:1.75; color:#2c3e50;">内容...</div>
  </div>
</div>
```

---

## 四、视觉设计规范

### 字体与色彩

```css
font-family: 'Georgia', 'Songti SC', serif;

/* 语义化配色 */
蓝  #1a5276 / #ebf5fb  →  定义、理论
金  #9a7b0a / #fef9e7  →  类比、直觉
绿  #1e8449 / #eafaf1  →  结论、正确
紫  #8e44ad / #f5eef8  →  公式、数学
红  #c0392b / #fdedec  →  警告、误区
主渐变  #667eea → #764ba2
```

### 双层容器（必须）

```html
<div style="background:linear-gradient(135deg,#667eea,#764ba2);
            padding:3px; border-radius:20px;
            box-shadow:0 20px 60px rgba(102,126,234,0.25);">
  <div style="background:white; border-radius:18px;
              min-height:540px; padding:36px 44px;
              display:flex; flex-direction:column; gap:22px;">
    <!-- 内容 -->
  </div>
</div>
```

### 排版

| 元素 | 规格 |
|------|------|
| 大标题 | 1.55em, bold, #1a5276 |
| 卡片标题 | 1em, bold, 对应语义色 |
| 卡片正文 | 1.15em, line-height 1.8, #2c3e50 |
| 次要说明 | 1em, #666 |
| 最小字号 | 1em（不得更小） |

### Tab 样式

```css
.tab {
  padding: 8px 18px; border-radius: 20px; border: 2px solid #ddd;
  background: white; cursor: pointer; font-size: 1em;
  color: #888; transition: all 0.2s; font-family: inherit;
}
.tab.active {
  background: #667eea; color: white; border-color: #667eea;
}
.tab-panel { animation: fadeIn 0.3s ease; }
.tab-panel.hidden { display: none; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; } }
```

---

## 五、MathJax 正确初始化

**必须严格按此顺序，否则公式无法渲染。**

### head 中（先配置再加载）

```html
<script>
window.MathJax = {
  tex: {
    inlineMath: [['\\(','\\)']],
    displayMath: [['\\[','\\]']]
  },
  startup: { typeset: false }   /* 禁用自动排版，手动控制 */
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
```

### showSlide() 中

```js
function showSlide(idx) {
  current = idx;
  const container = document.getElementById(CONTAINER_ID);
  container.innerHTML = slides[idx];
  // ... 更新页码、dots、按钮 ...

  // 公式渲染
  if (window.MathJax && MathJax.typesetPromise) {
    MathJax.typesetPromise([container]).catch(err => console.warn(err));
  }
  // Mermaid 图表渲染
  const mEls = container.querySelectorAll('.mermaid');
  if (mEls.length) {
    mEls.forEach(el => el.removeAttribute('data-processed'));
    mermaid.init(undefined, mEls);
  }
}
```

### 初始化第一页（等待 MathJax 加载完成）

```js
window.addEventListener('load', function () {
  if (window.MathJax?.startup?.promise) {
    MathJax.startup.promise.then(() => showSlide(0));
  } else {
    showSlide(0);
  }
});
```

❌ **禁止**在脚本底部直接调用 `showSlide(0)`，MathJax 此时尚未加载。

---

## 六、导航规范

```js
// dots 构建
function buildDots() {
  const el = document.getElementById('dots');
  el.innerHTML = '';
  slides.forEach((_, i) => {
    const d = document.createElement('div');
    d.style.cssText = 'width:10px;height:10px;border-radius:50%;cursor:pointer;transition:all 0.25s;';
    d.style.background = i === current ? '#667eea' : '#ccc';
    d.style.transform = i === current ? 'scale(1.4)' : 'scale(1)';
    d.onclick = () => showSlide(i);
    el.appendChild(d);
  });
}
```

- 首页「上一页」禁用（opacity 0.3）
- 末页「下一页」禁用（opacity 0.3）
- 右上角页码「N / Total」，颜色 #aaa

---

## 七、防冲突规范

slides 数组用模板字面量（反引号）：
```js
const slides = [
  `<div>第一页</div>`,
  `<div>第二页</div>`
];
```

- `<pre><code>` 中**禁止**使用反引号
- Python 代码中的文档注释用 `'''` 替代 `"""`
- 容器 ID：`'slide_' + Math.random().toString(36).slice(2, 10)`

---

## 八、完整骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>主题</title>
<script>
window.MathJax = {
  tex: { inlineMath: [['\\(','\\)']], displayMath: [['\\[','\\]']] },
  startup: { typeset: false }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  font-family: 'Georgia', 'Songti SC', serif;
  background: linear-gradient(160deg, #f0f2f8, #e8eaf6);
  min-height: 100vh;
  display: flex; flex-direction: column; align-items: center;
  padding: 36px 20px 48px;
}
.wrapper { width: 100%; max-width: 920px; }
button { font-family: inherit; }
pre { background: white !important; color: #2c3e50 !important;
      font-family: 'Courier New', monospace; font-size: 1em;
      line-height: 1.65; border-radius: 10px; padding: 16px 20px;
      border: 1px solid #e0e0e0; overflow-x: auto; }
code { background: #f4f4f4 !important; color: #2c3e50 !important;
       font-family: 'Courier New', monospace; font-size: 0.95em;
       border-radius: 4px; padding: 1px 5px; }
.tab { padding:8px 18px; border-radius:20px; border:2px solid #ddd;
       background:white; cursor:pointer; font-size:1em; color:#888;
       transition:all 0.2s; font-family:inherit; }
.tab.active { background:#667eea; color:white; border-color:#667eea; }
.tab-panel { animation: fadeIn 0.3s ease; }
.tab-panel.hidden { display:none; }
.step { animation: fadeIn 0.4s ease; }
.step.hidden { display:none; }
@keyframes fadeIn { from { opacity:0; transform:translateY(6px); } to { opacity:1; } }
</style>
</head>
<body>
<div class="wrapper">
  <div style="background:linear-gradient(135deg,#667eea,#764ba2);
              padding:3px;border-radius:20px;
              box-shadow:0 20px 60px rgba(102,126,234,0.25);">
    <div id="SLIDE_ID"
         style="background:white;border-radius:18px;
                min-height:540px;padding:36px 44px;
                display:flex;flex-direction:column;gap:22px;">
    </div>
  </div>
  <div style="display:flex;justify-content:space-between;
              align-items:center;margin-top:20px;padding:0 2px;">
    <button id="btn-prev" onclick="navigate(-1)"
      style="padding:11px 30px;border-radius:25px;border:none;
             background:#ecf0f1;color:#7f8c8d;font-size:1em;cursor:pointer;">
      ← 上一页</button>
    <div id="dots" style="display:flex;gap:9px;"></div>
    <button id="btn-next" onclick="navigate(1)"
      style="padding:11px 30px;border-radius:25px;border:none;
             background:linear-gradient(135deg,#667eea,#764ba2);
             color:white;font-size:1em;cursor:pointer;">
      下一页 →</button>
  </div>
</div>
<script>
mermaid.initialize({ startOnLoad: false, theme: 'default' });
const CONTAINER_ID = 'SLIDE_ID';
const slides = [
  `...slide 0 HTML...`,
  `...slide 1 HTML...`,
];
let current = 0;

function buildDots() {
  const el = document.getElementById('dots');
  el.innerHTML = '';
  slides.forEach((_, i) => {
    const d = document.createElement('div');
    d.style.cssText = 'width:10px;height:10px;border-radius:50%;cursor:pointer;transition:all 0.25s;';
    d.style.background = i === current ? '#667eea' : '#ccc';
    d.style.transform = i === current ? 'scale(1.4)' : 'scale(1)';
    d.onclick = () => showSlide(i);
    el.appendChild(d);
  });
}

function showSlide(idx) {
  current = idx;
  const c = document.getElementById(CONTAINER_ID);
  c.innerHTML = slides[idx];
  const pn = c.querySelector('#pn');
  if (pn) pn.textContent = (idx + 1) + ' / ' + slides.length;
  document.getElementById('btn-prev').style.opacity = idx === 0 ? '0.3' : '1';
  document.getElementById('btn-next').style.opacity = idx === slides.length-1 ? '0.3' : '1';
  document.getElementById('btn-prev').disabled = idx === 0;
  document.getElementById('btn-next').disabled = idx === slides.length-1;
  buildDots();
  if (window.MathJax?.typesetPromise) MathJax.typesetPromise([c]).catch(console.warn);
  const mEls = c.querySelectorAll('.mermaid');
  if (mEls.length) { mEls.forEach(e => e.removeAttribute('data-processed')); mermaid.init(undefined, mEls); }
}

function navigate(dir) {
  const n = current + dir;
  if (n >= 0 && n < slides.length) showSlide(n);
}

window.addEventListener('load', () => {
  if (window.MathJax?.startup?.promise) MathJax.startup.promise.then(() => showSlide(0));
  else showSlide(0);
});
</script>
</body>
</html>
```
