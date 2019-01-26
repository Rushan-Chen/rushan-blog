---
layout: post
title: "`var`和重复声明"
subtitle: "`var` and repeat declaration"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - 变量声明
---

[重复声明函数 — JavaScript社区](https://xugaoyang.com/post/QK28yZvbj6)

在线代码：[var重复声明](https://jsbin.com/qaveqopayo/edit?js,console)

```js
//var声明并赋值
// 例1
function f1 () {
  console.log('function');
}
var f1 = true;
console.log(typeof f1); // "boolean"

// 例2
var f2 = true;
function f2 () {
  console.log('function');
}
console.log(typeof f2); // "boolean"

```

例2等价于例1，函数语法声明会提前到开头。**变量查找规则是由内到外，由近到远。** 近的是布尔值。

```js
//var声明
// 例3
function f3 (){}
var f3;
console.log(typeof f3); // "function"

// 例4
var f4;
function f4 (){}
console.log(typeof f4); // "function"

```

例4等价于例3，函数语法声明会提前到开头。

`var`在编译期也会在顶部声明，默认值是`undefined`。

所以是`function` 还是`undefined`？

**JS中，函数是【一等公民】，比变量有更高的优先级。
所以是`function`。**

> 变量是所有编程语言的基石，应该是默认的高等公民。
> 在JS中说函数是一等公民主要原因是：函数不仅是函数，也可以构建对象，而对象是JS的基础，其他语言的函数没这个功能。

## 如何避免`var`带来的这种问题？

> 能用`let`就不用`var`