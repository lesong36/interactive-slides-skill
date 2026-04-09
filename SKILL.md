---
name: interactive-slides
description: "生成交互式 HTML 幻灯片演示文稿，适合用于教学、学术讲解、课程内容展示。当用户提供学习材料、笔记、课程内容、知识点，并要求生成幻灯片、演示文稿、slides、交互式讲义时，必须使用此技能。触发关键词包括：幻灯片、slides、演示文稿、交互式幻灯片、课件、教学课件、interactive slides、slideshow。即使用户只说「帮我做成幻灯片」或「把这个内容做成slides」也应触发此技能。"
---

# Interactive Slides Skill

当用户提供上下文内容，生成一套可在浏览器中交互浏览的 HTML 幻灯片。

## 工作流程

1. 仔细阅读用户提供的上下文
2. 规划幻灯片结构：识别核心概念，每个概念独立一幕
3. 生成完整的单文件 HTML（所有 JS/CSS 内联），可直接双击在浏览器中打开

---

## 内容结构规范

### 每张幻灯片的标准结构

每个核心概念一幕，包含以下 **3 个固定模块**（不可省略）：

```
① 定义卡片   — 用一句话给出概念的精准定义（不超过 30 字）
② 类比/直觉  — 用生活中的例子或类比让概念「活起来」（必须有，不可用抽象描述代替）
③ 公式或图示 — 公式用 MathJax 渲染，图示用 emoji + 箭头符号
```

- 如有代码示例，单独开一幕
- 如有复杂推导步骤，每步一幕，不要在一页中堆砌多步

### 文字密度（极重要）

**每张幻灯片最多 3 张卡片，每张卡片最多 3 行文字。** 这是硬性约束，不是建议。

- 定义只需一句话，不要写成段落
- 不用解释所有细节，挑最重要的说
- 宁可多开一幕，绝不在一页内塞下所有内容
- 禁止出现超过 3 行的纯文字段落

### 类比要求（必须执行）

每个抽象概念幕必须有一个 **具体的生活类比**，格式为：

```
💡 类比：[生活场景] → [概念映射]
例：「先验概率就像法官在开庭前基于案件历史的初始判断」
```

类比必须具体、形象，不能用「类似于...的概念」这种模糊表述。

### 术语规范

- 首次出现时格式：**概念名（English Name）**
- 立刻给出一句话中文定义，不要跳过

---

## 视觉设计规范

### 字体与色彩

```css
font-family: 'Georgia', 'Songti SC', serif;

/* 语义化配色 */
--blue:   #1a5276;  --blue-bg:   #ebf5fb;   /* 定义/理论 */
--gold:   #9a7b0a;  --gold-bg:   #fef9e7;   /* 类比/直觉 */
--green:  #1e8449;  --green-bg:  #eafaf1;   /* 结论/结果 */
--purple: #8e44ad;  --purple-bg: #f5eef8;   /* 公式/数学 */
--red:    #c0392b;  --red-bg:    #fdedec;   /* 警告/误区 */
--grad-a: #667eea;  --grad-b:    #764ba2;   /* 主渐变 */
```

### 幻灯片容器（必须使用双层结构）

```html
<!-- 外层：渐变边框 -->
<div style="background: linear-gradient(135deg, #667eea, #764ba2);
            padding: 3px; border-radius: 20px;
            box-shadow: 0 20px 60px rgba(102,126,234,0.25);">
  <!-- 内层：白色背景 -->
  <div style="background: white; border-radius: 18px;
              min-height: 540px; padding: 36px 44px;
              display: flex; flex-direction: column; gap: 24px;">
    <!-- 内容 -->
  </div>
</div>
```

### 卡片样式（每张卡片）

```html
<div style="background: #ebf5fb; border-left: 4px solid #1a5276;
            border-radius: 12px; padding: 18px 22px;">
  <div style="font-size: 1em; font-weight: 700; color: #1a5276;
              margin-bottom: 8px;">卡片标题</div>
  <div style="font-size: 1.15em; line-height: 1.8; color: #2c3e50;">
    内容（最多 3 行）
  </div>
</div>
```

### 排版规范

| 元素 | 字号 | 颜色 |
|------|------|------|
| 幻灯片大标题 | 1.55em, bold | #1a5276 |
| 卡片标题 | 1em, bold | 对应语义色 |
| 卡片正文 | 1.15em | #2c3e50 |
| 页码 | 0.95em | #aaa |

- 最小字号不得低于 1em
- 行高统一 1.8
- 卡片之间 `gap: 22px`
- 幻灯片内容区 `padding: 36px 44px`
- `max-width: 920px`，居中显示

### 公式区块

```html
<div style="background: #f5eef8; border-radius: 12px;
            padding: 20px; text-align: center;
            font-size: 1.2em; border: 1px solid #d2b4de;">
  \[ 公式 \]
</div>
```

---

## 导航与交互

### 进度条

```html
<div style="display: flex; justify-content: center; gap: 8px;
            background: transparent; padding: 6px 0;">
  <!-- 每页一个圆点 -->
  <div onclick="showSlide(i)"
       style="width: 10px; height: 10px; border-radius: 50%;
              background: #ccc; cursor: pointer; border: none;
              transition: all 0.25s;">
  </div>
  <!-- 当前页圆点: background: #667eea; transform: scale(1.4) -->
</div>
```

### 导航按钮

```html
<!-- 上一页（禁用时 opacity: 0.3） -->
<button style="padding: 10px 28px; border-radius: 25px; border: none;
               background: #ecf0f1; color: #7f8c8d; font-size: 1em; cursor: pointer;">
  ← 上一页
</button>

<!-- 下一页（渐变色） -->
<button style="padding: 10px 28px; border-radius: 25px; border: none;
               background: linear-gradient(135deg, #667eea, #764ba2);
               color: white; font-size: 1em; cursor: pointer;">
  下一页 →
</button>
```

- 首页禁用「上一页」，末页禁用「下一页」
- 右上角显示页码「N / Total」，颜色 #aaa

---

## 【关键】MathJax 正确初始化方式

> 这是公式渲染失败的根本原因。必须严格按以下模式，不可简化。

### 第一步：在 `<head>` 中先配置再加载

```html
<head>
<!-- 必须先配置，再加载脚本 -->
<script>
window.MathJax = {
  tex: {
    inlineMath: [['\\(', '\\)']],
    displayMath: [['\\[', '\\]']]
  },
  startup: { typeset: false }   /* 禁止自动排版，由我们手动控制 */
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
</head>
```

### 第二步：`showSlide()` 函数

```js
function showSlide(idx) {
  current = idx;
  const container = document.getElementById(CONTAINER_ID);
  container.innerHTML = slides[idx];

  // 更新页码、进度点、按钮禁用状态...

  // 手动触发当前幻灯片的公式渲染
  if (window.MathJax && MathJax.typesetPromise) {
    MathJax.typesetPromise([container]).catch(err => console.warn(err));
  }
}
```

### 第三步：等待 MathJax 就绪后再初始化第一页

```js
// ✅ 正确：等 MathJax startup promise resolve 后再渲染第一页
window.addEventListener('load', function() {
  if (window.MathJax && MathJax.startup && MathJax.startup.promise) {
    MathJax.startup.promise.then(function() { showSlide(0); });
  } else {
    showSlide(0);
  }
});
```

**❌ 错误的做法（会导致公式渲染失败）：**
```js
showSlide(0);  // 不要在脚本底部直接调用！MathJax 此时尚未加载完成。
```

---

## Mermaid 图表（如有因果图/流程图）

```html
<head>
  <script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
</head>
```

在 `showSlide()` 末尾：
```js
const mermaidEls = container.querySelectorAll('.mermaid');
if (mermaidEls.length > 0) {
  mermaid.init(undefined, mermaidEls);
}
```

---

## 防字符串嵌套冲突

slides 数组中每条 slide 用模板字面量（反引号）：

```js
const slides = [
  `<div>第一页内容</div>`,
  `<div>第二页内容</div>`
];
```

- 在 HTML 内部的 `<pre><code>` 中，**禁止**出现反引号（`` ` ``）
- Python 代码示例中的多行字符串/文档注释，用三重单引号 `'''...'''` 替代三重双引号

---

## 容器唯一 ID

```js
const CONTAINER_ID = 'slide_' + Math.random().toString(36).slice(2, 10);
```

---

## 输出格式

写入 `.html` 文件（内容较多时），告知用户文件路径，用户双击即可在浏览器中打开。

---

## 一个完整骨架示例

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>主题名称</title>
<script>
window.MathJax = {
  tex: { inlineMath: [['\\(','\\)']], displayMath: [['\\[','\\]']] },
  startup: { typeset: false }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  font-family: 'Georgia', 'Songti SC', serif;
  background: #f0f2f8;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 32px 20px;
}
.wrapper { width: 100%; max-width: 920px; }
pre, code { background: white !important; color: #2c3e50 !important;
            font-family: 'Courier New', monospace; font-size: 1em; }
</style>
</head>
<body>
<div class="wrapper">
  <!-- 渐变外框 -->
  <div style="background: linear-gradient(135deg,#667eea,#764ba2);
              padding: 3px; border-radius: 20px;
              box-shadow: 0 20px 60px rgba(102,126,234,0.25);">
    <div id="SLIDE_CONTAINER"
         style="background: white; border-radius: 18px;
                min-height: 540px; padding: 36px 44px;
                display: flex; flex-direction: column; gap: 24px;">
    </div>
  </div>

  <!-- 导航 -->
  <div style="display: flex; justify-content: space-between;
              align-items: center; margin-top: 20px; padding: 0 4px;">
    <button id="btn-prev" onclick="navigate(-1)"
      style="padding:10px 28px;border-radius:25px;border:none;
             background:#ecf0f1;color:#7f8c8d;font-size:1em;
             cursor:pointer;font-family:inherit;">← 上一页</button>

    <div id="dots" style="display:flex;gap:8px;background:transparent;"></div>

    <button id="btn-next" onclick="navigate(1)"
      style="padding:10px 28px;border-radius:25px;border:none;
             background:linear-gradient(135deg,#667eea,#764ba2);
             color:white;font-size:1em;cursor:pointer;font-family:inherit;">
      下一页 →</button>
  </div>
</div>

<script>
const CONTAINER_ID = 'SLIDE_CONTAINER';
const slides = [
  /* slide 0 */
  `<div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:4px;">
     <div style="font-size:1.55em;font-weight:bold;color:#1a5276;">幻灯片标题</div>
     <div style="font-size:0.95em;color:#aaa;" id="page-num"></div>
   </div>
   <!-- 卡片1: 定义 -->
   <div style="background:#ebf5fb;border-left:4px solid #1a5276;
               border-radius:12px;padding:18px 22px;">
     <div style="font-size:1em;font-weight:700;color:#1a5276;margin-bottom:8px;">📖 定义</div>
     <div style="font-size:1.15em;line-height:1.8;color:#2c3e50;">一句话定义。</div>
   </div>
   <!-- 卡片2: 类比 -->
   <div style="background:#fef9e7;border-left:4px solid #9a7b0a;
               border-radius:12px;padding:18px 22px;">
     <div style="font-size:1em;font-weight:700;color:#9a7b0a;margin-bottom:8px;">💡 直觉类比</div>
     <div style="font-size:1.15em;line-height:1.8;color:#2c3e50;">具体的生活类比。</div>
   </div>
   <!-- 公式 -->
   <div style="background:#f5eef8;border-radius:12px;padding:20px;
               text-align:center;font-size:1.15em;border:1px solid #d2b4de;">
     \\[ 公式 \\]
   </div>`,
];

let current = 0;

function buildDots() {
  const el = document.getElementById('dots');
  el.innerHTML = '';
  slides.forEach((_, i) => {
    const d = document.createElement('div');
    d.style.cssText = `width:10px;height:10px;border-radius:50%;cursor:pointer;transition:all 0.25s;`;
    d.style.background = i === current ? '#667eea' : '#ccc';
    d.style.transform = i === current ? 'scale(1.4)' : 'scale(1)';
    d.onclick = () => showSlide(i);
    el.appendChild(d);
  });
}

function showSlide(idx) {
  current = idx;
  const container = document.getElementById(CONTAINER_ID);
  container.innerHTML = slides[idx];

  const pn = container.querySelector('#page-num');
  if (pn) pn.textContent = (idx + 1) + ' / ' + slides.length;

  document.getElementById('btn-prev').disabled = idx === 0;
  document.getElementById('btn-next').disabled = idx === slides.length - 1;
  document.getElementById('btn-prev').style.opacity = idx === 0 ? '0.3' : '1';
  document.getElementById('btn-next').style.opacity = idx === slides.length - 1 ? '0.3' : '1';

  buildDots();

  if (window.MathJax && MathJax.typesetPromise) {
    MathJax.typesetPromise([container]).catch(err => console.warn(err));
  }
}

function navigate(dir) {
  const n = current + dir;
  if (n >= 0 && n < slides.length) showSlide(n);
}

window.addEventListener('load', function() {
  if (window.MathJax && MathJax.startup && MathJax.startup.promise) {
    MathJax.startup.promise.then(function() { showSlide(0); });
  } else {
    showSlide(0);
  }
});
</script>
</body>
</html>
```
