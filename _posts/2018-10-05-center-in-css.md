---
layout: post
title: "【译】CSS 居中：完全指南"
subtitle: "Centering in CSS"
author: "Rushan"
header-img: "img/post-bg-css-logo.jpg"
header-img-credit: "Photo by Brandon Morelli"
header-img-credit-href: "https://codeburst.io/master-css-learn-css3-css4-flexbox-animations-grids-frameworks-and-more-dbf69ac4ff61"
header-mask: 0.4
tags:
  - CSS
---

【目录】

- [1 水平居中](#1)
  - 1.1 是 `inline`/`inline-*` 元素（比如文本或链接）？
  - 1.2 是块级元素？
  - 1.3 有多个块级元素？
- [2 垂直居中](#2)
  - 2.1 是 `inline`/`inline-*` 元素（比如文本或链接）？
    - 2.1.1 单行？
    - 2.1.2 多行？
  - 2.2 是块级元素？
    - 2.2.1 已知元素 `height` ?
    - 2.2.2 未知元素 `height`?
    - 2.2.3 能用 flexbox ?
- [3 水平&垂直居中](#3)
  - 3.1 有固定 `width` 和 `height` ？
  - 3.2 未知 `width` 和 `height`？
  - 3.3 能用 flexbox ?
  - 3.4 能用 grid ？
- [4 总结](#4)

原文：[Centering in CSS: A Complete Guide - css-tricks](https://css-tricks.com/centering-css-complete-guide/)

CSS 居中是 CSS 中时常遇到的需求，也时常令人抓狂 🙃。🙂 为什么要这么难搞呢？作者认为这个问题不是很难解决，而是有很多不同的方法，需要看情况，很难知道哪种适用。

所以我们把它变成一个决策树 🌲，希望能让它变得容易些。

💆 我要 center……

## <span id="1">1 水平居中</span> 

### 1.1 是 `inline`/`inline-*` 元素（比如文本或链接）？

（译者注：[inline element — MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Inline_elemente)）

实现块级父元素里的内联（行内）元素的水平居中，只需：

```css
.center-children {
  text-align: center;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/HulzB)

![1.1](https://ws1.sinaimg.cn/large/006tNbRwgy1fxj6159qztj31fk0iutc7.jpg)

这适用于`inline`、`inline-block`、`inline-table`、`inline-flex`等。

### 1.2 是块级元素？

（译者注：[Block level element — MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Block-level_elements)）

对于块级元素，可以通过设置 `margin-left` 和 `margin-right` 为 `auto`，来实现居中（并且元素已设置了 `width`，不然它就是全宽，不需要居中）。通常这样简写：

```css
.center-me {
  margin: 0 auto;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/eszon)

![1.2](https://ws3.sinaimg.cn/large/006tNbRwgy1fxj5z88rhaj31920h6wgw.jpg)

这适用于块级元素，无论子元素/父元素的`width`是多少。

注意，不能用`float`居中一个元素。虽然还是有一招：[Faking ‘float: center’ with Pseudo Elements — css-tricks](https://css-tricks.com/float-center/)。

### 1.3 有多个块级元素？

如果有 2 个或多个块级元素需要 **在一行中** 水平居中，那改变它们的 `display` 类型可能会更好。这里有一个改为 `inline-block` 的例子，和一个改为 `flexbox` 的例子：

CodePen: [Link](https://codepen.io/chriscoyier/pen/ebing?editors=1100)

![1.3.1](https://ws4.sinaimg.cn/large/006tNbRwgy1fxj6hl3r98j31780u0jyp.jpg)

除非你是要有多个块级元素堆叠在一起，那么在这种情况下，auto margin 仍然适用：

CodePen: [Link](https://codepen.io/chriscoyier/pen/haCGt)

![1.3.2](https://ws3.sinaimg.cn/large/006tNbRwgy1fxj6s0znddj31m00oudk4.jpg)

---

## <span id="2">2 垂直居中</span> 

垂直居中在 CSS 中就有点棘手啦 👻。

### 2.1 是 `inline`/`inline-*` 元素（比如文本或链接）？

#### 2.1.1 单行？

有时候内联/文本元素可以垂直居中，只是因为它们都有相等的上下 `padding`。

```css
.link {
  padding-top: 30px;
  padding-bottom: 30px;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/ldcwq?editors=1100)

![2.1.1](https://ws1.sinaimg.cn/large/006tNbRwgy1fxj6z9ij9gj31lw0f2418.jpg)

如果由于某种原因不能选择改 `padding`，这时要让（不会换行的）文本居中，有一个技巧，就是让 `line-hight` 等于 `height` 来实现文本垂直居中。

(译者注：`height` 是容器的高度，比如 `div` 的高度；`line-hight` 行高是一行  的高度（好像废话 😶）)

```css
.center-text-trick {
  height: 100px;
  line-height: 100px;
  white-space: nowrap;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/LxHmK?editors=1100)

![2.1.2](https://ws1.sinaimg.cn/large/006tNbRwgy1fxj8cuz2vsj31kk0n2q5d.jpg)

#### 2.1.2 多行？

有相等的上下 `padding`也可以实现多行文本居中，如果无效，那文本所在的元素可能是一个表格单元格，确实是或者表现得像表格单元格。在这种情况下，与单行文本居中的方式不同，要用 `vertical-align` 来处理（更多[关于`vertical-align`](https://css-tricks.com/what-is-vertical-align/)）。

（译者注：table 单元格里的内容，默认 `vertical-align: middle;`。要仿表格，就让父元素 `display: table;`，子元素 `display: table-cell; vertical-align: middle;`）。

CodePen: [Link](https://codepen.io/chriscoyier/pen/ekoFx)

![2.1.2](https://ws2.sinaimg.cn/large/006tNbRwgy1fxj8t65h99j31uw0u0q92.jpg)

### 2.2 是块级元素？

#### 2.2.1 已知元素 `height` ?

由于很多原因，不知道网页布局的高度是相当常见的：如果宽度发生变化，文本重排可以改变高度；文本样式的差异可以改变高度；文本量的差异可以改变高度；具有固定宽高比的元素（如图像）可在调整大小时更改高度等等。

但如果你知道高度 👀，你可以这样来垂直居中：

```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  height: 100px;
  margin-top: -50px; /* 如果不是用`box-sizing: border-box;`，数值还要减去`padding`和`border` */
}
```

（译者注：`box-sizing`，默认值`content-box`，`width` = 内容的宽度，`height` = 内容的高度。宽度和高度都不包含内容的`border`和`padding`。而可选值`border-box`，`width`和`height`属性包括内容，`border`和`padding`三者，但不包括`margin`。参考[box-sizing — MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing)。一些专家建议都用`border-box` ([Link](https://css-tricks.com/international-box-sizing-awareness-day/))。）

CodePen: [Link](https://codepen.io/chriscoyier/pen/HiydJ)

（译者注：`box-sizing` 为默认值的情况下）
![2.2.1-1](https://ws4.sinaimg.cn/large/006tNbRwgy1fxjato5zgzj31ni0o2wku.jpg)

（译者加：`box-sizing` 为 `border-box` 的情况下）
![2.2.1-2](https://ws2.sinaimg.cn/large/006tNbRwgy1fxjaw83y44j31ne0o4jw8.jpg)

#### 2.2.2 未知元素 `height`?

当高度不确定，也还是  有可能通过“先  往下推到一半，再  向上太高它一半的高度”的方法实现居中：

```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}
```

（译者注：[translateY — MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/translateY)，沿 Y 轴（垂直方向）平移，往下为正，往上为负。百分比基于元素当前的高度）

CodePen: [Link](https://codepen.io/chriscoyier/pen/lpema)

![2.2.2](https://ws1.sinaimg.cn/large/006tNbRwgy1fxjbtvklb7j31ne0osn0m.jpg)

#### 2.2.3 能用 flexbox ?

不出意料，用 flexbox 就简单得多了。

（译者注：flexbox 的教程见 [Flex 布局教程 — 阮一峰](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)；flexbox 的浏览器兼容性见：[flexbox — CanIUse](https://caniuse.com/#feat=flexbox)）

```css
.parent {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/FqDyi)

![2.2.3](https://ws3.sinaimg.cn/large/006tNbRwgy1fxjc922e1nj31ne0p2jus.jpg)

---

## <span id="3">3 水平&垂直居中</span> 

你可以以任何方式组合  上述方法，以获得完美的中心元素 ❤️。但作者发现，这通常分为 4 大阵营：

### 3.1 有固定 `width` 和 `height` ？

在绝对定位到 50%/50% 之后，用相当于一半宽度和一半高度的负`margin`，就能实现中心居中，且具有很好的跨浏览器兼容性：

```css
.parent {
  position: relative;
}
.child {
  width: 300px;
  height: 100px;
  padding: 20px;

  position: absolute;
  top: 50%;
  left: 50%;

  margin: -70px 0 0 -170px;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/JGofm)

![3.1](https://ws3.sinaimg.cn/large/006tNbRwgy1fxjcr63vasj31nc0o6n1d.jpg)

### 3.2 未知 `width` 和 `height`？

如果宽度和高度未知，可以使用`trasform`属性和两个方向的负平移 50%（基于元素当前的宽度/ 高度）来实现居中：

```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/lgFiq)

![3.2](https://ws3.sinaimg.cn/large/006tNbRwgy1fxjczit7srj31ng0o6q6n.jpg)

### 3.3 能用 flexbox ?

用 flexbox 实现两个方向上的居中，要用到使两个属性的值为`center`：

```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/msItD)

![3.3](https://ws1.sinaimg.cn/large/006tNbRwgy1fxjd8w38wtj31nc0o6gpa.jpg)

### 3.4 能用 grid ？

这就是小技巧啦，对单个元素很有效（由 Lance Janssen 添加）：

（译者注：Grid 浏览器兼容性见 [grid — CanIUse](https://caniuse.com/#search=grid)）

```css
body,
html {
  height: 100%;
  display: grid;
}
span {
  /* 要居中的元素 */
  margin: auto;
}
```

CodePen: [Link](https://codepen.io/chriscoyier/pen/NvwpyK)

![3.4](https://ws1.sinaimg.cn/large/006tNbRwgy1fxjdf83bqmj31kc0gsmyr.jpg)

## <span id="4">4 总结</span>

你完全可以实现居中 😉。
