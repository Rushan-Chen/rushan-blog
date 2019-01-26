---
layout: post
title: "forEach VS reduce | Array"
subtitle: "Compare array.forEach and array.reduce"
author: "Rushan"
header-img: "img/post-bg-default.jpg"
header-img-credit: "Photo by Andy Holmes on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/LUpDjlJv4_c"
header-mask: 0.4
tags:
  - JavaScript
  - Array
---

[对比forEach和reduce — JavaScript社区](https://xugaoyang.com/post/0zwtDxaHGu)

在线代码：[array.reduce()](https://jsbin.com/tapiricaba/edit?js,console)

文档：[Array.prototype.reduce() — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)


> arr.reduce(callback[, initialValue])
> 
> callback(accumulator, currentValue [, currentIndex [, array])

## 简单用法

```js
let list = [1, 2, 3, 4, 5, 6, 7 ];

// 第2个参数为0
let total = list.reduce((sum, value) => sum + value, 0);
console.log(`${list.join('+')} = ${total}`); // "1+2+3+4+5+6+7 = 28"

// 省略第2个参数
console.log(list.reduce((sum, value) => sum + value)); // 28

```

省略`reduce`的第2个参数(`initialValue`)，第一次执行时，回调函数的累加器(`accumulator`)的初始值，会是数组第1个值(索引0)，当前处理的元素会是第2个值(索引1)。

这时循环的次数少一次，相比`for`循环，或者`reduce`第2个参数设置为0的情况。(少了0+1这一步)

但注意，如果数组是空数组，记得带上初始值，否则会报错。所以，**提供初始值通常更安全**。

## 进阶用法（来源：参考资料）

一个面试题，问如何知道一串字符串中每个字母出现的次数？先自己捣鼓一下呗~

```js
// 如何知道一串字符串中每个字母出现的次数？
const arrString = 'accdaabc';
```

出答案前先插播一下`string.split()`:

```js
// 插播 string.split()
console.log(arrString.split('')); // ["a", "c", "c", "d", "a", "a", "b", "c"]
console.log(arrString); // split不改变原数组
```

一种解法是：

```js
// 字符串 -> 数组 -> 对象
// 当前字符串是否存在于res对象的属性中？
// 存在，把它的值+1；
// 不存在，把它新增到对象res的属性里，初始值为1。

arrString.split('').reduce((res, cur) => {
  console.log(res, cur);
  res[cur] ? res[cur] ++ : res[cur] = 1;
  return res;
}, {});

// 一下子看不懂，一步步来呗 ~
// // index =0
// {}['a']; // undefined
// {}['a'] = 1; // {a:1}

// // index = 1
// {a:1}['c']; // undefined
// {a:1}['c'] = 1; // {a:1, c:1}

// // index = 2
// {a:1, c:1}['c']; // 1
// {a:1, c:1}['c'] ++; // {a:1, c:2}

// // index = 3
// {a:1, c:2}['d']; // undefined
// {a:1, c:2}['d'] = 1; // {a:1, c:2, d:1}

// //…
// {a:3, c:3, d:1, b:1};

```

![log](https://ws3.sinaimg.cn/large/0069RVTdgy1ftz2889d94j30z60i240y.jpg)

> 由于可以通过第二参数设置叠加结果的类型初始值，因此这个时候`reduce`就不再仅仅只是做一个加法了，我们可以灵活的运用它来进行各种各样的类型转换，比如将数组按照一定规则转换为对象，也可以将一种形式的数组转换为另一种形式的数组。

题目其他人的解法：[面试题：用js实现读取出字符串中每个字符重复出现的次数？— segmentfault](https://segmentfault.com/q/1010000005070166)

## 参考资料

[数组reduce方法的高级技巧 — segmentfault](https://segmentfault.com/a/1190000005921341)