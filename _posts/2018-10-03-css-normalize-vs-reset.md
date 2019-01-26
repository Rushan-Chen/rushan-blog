---
layout: post
title: "Normalize.css VS Reset.css"
subtitle: ""
author: "Rushan"
header-img: "img/post-bg-css-logo.jpg"
header-img-credit: "Photo by Brandon Morelli"
header-img-credit-href: "https://codeburst.io/master-css-learn-css3-css4-flexbox-animations-grids-frameworks-and-more-dbf69ac4ff61"
header-mask: 0.4
tags:
  - CSS
---

## Normalize

[Normalize.css](https://github.com/necolas/normalize.css): 使正常化，标准化。

> Normalize.css makes browsers render all elements more consistently and in line with modern standards. It precisely targets only the styles that need normalizing.

使浏览器更加一致地呈现所有元素并符合现代标准。它只针对需要规范化的样式。

- 保留浏览器默认样式中有用的部分

- 同时保证跨浏览器一致性（修复各浏览器中存在的不合W3C规范的问题）

- 在合适的地方设置默认值

另外，Normalize是模块化的，有详细的注释说明代码的作用，更便于自定义，比如移除一些不需要的。

## Reset

[Reset.css](https://meyerweb.com/eric/tools/css/reset/): 重置，清零。

强制使所有浏览器表现一致，去掉大多数浏览器的默认样式。

## 直观比较

从Zach Wolf的[Normalize VS Rest](https://codepen.io/zachwolf/full/bdZMZj/)直观地看看二者的区别：

![Normalize VS Rest](https://ws3.sinaimg.cn/large/006tNbRwgy1fxi09tebilj30yz0u0gv0.jpg)

## 怎么选

一般选择Normalize.css。原因：

- 不用再为所有公共的排版元素重新设置样式。
  看上图右边的内容，要重写所有的h1, h2, ul,li等样式，大概生无可恋...
- 没有大段的继承链，不会使调试变得复杂。
- 便于自定义，在业务中基本不会用到的样式可以去掉，比如`legend`、`fieldset`等。

更好的选择是，为业务量身定制。

## 参考

- [Reset.css](https://meyerweb.com/eric/tools/css/reset/)
- [Normalize.css](https://github.com/necolas/normalize.css)
- [Normalize VS Rest](https://codepen.io/zachwolf/full/bdZMZj/)