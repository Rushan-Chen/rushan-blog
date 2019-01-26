---
layout: post
title: "深拷贝 & 浅拷贝"
subtitle: "Deep clone and shadow copy"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
---

> `Object.assign(target, ...sources)` 用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。

> `JSON.stringify(value[, replacer [, space]])` 将一个 JavaScript 值(对象或者数组)转换为一个 JSON 字符串。

> `JSON.parse(text[, reviver])` 用来解析 JSON 字符串，构造由字符串描述的 JavaScript 值或对象。

代码：[浅拷贝&深拷贝](https://jsbin.com/rimapoqute/edit?js,console)

## 对象深拷贝的方法

- `Object.assign`

```js
const obj = { key: "deepClone" };
const objCopy1 = Object.assign({}, obj);
```

- `JSON.parse`与`JSON.stringify`

```js
const objCopy2 = JSON.parse(JSON.stringify(obj));
```

## 两种方法的使用范围

下面例子是在[Object.assign() — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)里的“深拷贝问题”例子基础上修改的。

```js
function test() {
  "use strict";

  let obj1 = { a: 0, b: { c: 0 }, d: [] }; // 原对象
  let obj2 = Object.assign({}, obj1); // Object.assign得到的新对象
  console.log(JSON.stringify(obj2));

  obj1.a = 1; // 改变原对象
  console.log(JSON.stringify(obj1.a)); // 1
  console.log(JSON.stringify(obj2.a)); // 0
  // number，复制值，各走各路

  obj2.a = 2; // 改变新对象
  console.log(JSON.stringify(obj1.a)); // 1
  console.log(JSON.stringify(obj2.a)); // 2
  // number，复制值，各走各路

  obj2.b.c = "obj shallow copy";
  console.log(JSON.stringify(obj1.b.c)); // 'obj shallow copy'
  console.log(JSON.stringify(obj2.b.c)); // 'obj shallow copy'
  // object，复制引用地址，一起变

  obj2.d[0] = "arr shallow copy";
  console.log(JSON.stringify(obj1.d[0])); // 'arr shallow copy'
  console.log(JSON.stringify(obj2.d[0])); // 'arr shallow copy'
  // array，复制引用地址，一起变

  // Deep Clone
  obj1 = { a: 0, b: { c: 0 }, d: [] };
  let obj3 = JSON.parse(JSON.stringify(obj1));
  obj1.a = 4;
  obj1.b.c = 4;
  console.log(JSON.stringify(obj3)); // 不变；{ a: 0, b: { c: 0}, d:[]}
}
test();
```

浅拷贝出现在拷贝 **引用类型** 的值，比如数组、对象。
(引用类型，参考https://github.com/wengjq/Blog/issues/3)

- 如果要复制的对象/数组中，含有 object/array 类型的值
  eg.`arr = [1, [2], 3];` `obj = {a:true, b:{}, c:arr};`
  则用`JSON.parse(JSON.stringify())`方法实现深拷贝;

- 如果只含有原始数据类型的值，比如 Boolean，Number，String。
  eg.`list = [false, 1, '非引用类型'];` `personObj = {name:'simon', age:17, student: true }；`
  则`Object.assign()`和`JSON.parse(JSON.stringify())`都不会出现浅拷贝的问题。

```js
let funObj = { a: 0, b: true, f: function() {} };
console.log(funObj);
console.log(JSON.stringify(funObj));

let obj4 = JSON.parse(JSON.stringify(funObj));
console.log(obj4);

// Deep clone
let obj5 = Object.assign({}, funObj);
console.log(obj5);
```

- 如果含有函数值，由于`JSON.stringify()`会忽略掉函数（当出现在对象中），或者将其改成`null`（当出现在数组中），用`Object.assign()`能把函数（地址）拷贝过来，用`JSON.parse(JSON.stringify())`不能。

## 参考

- [对象的浅拷贝 — JavaScript社区](https://xugaoyang.com/post/1VA0Yd9DYe)
- [赋值都是按值赋值的方式 — JavaScript社区](https://xugaoyang.com/post/nFZFcCGuYi)