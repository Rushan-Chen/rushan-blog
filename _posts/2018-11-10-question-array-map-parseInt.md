---
layout: post
title: '【小题一道】array.map(parseInt)'
subtitle: 'Question about array.map and parseInt'
author: 'Rushan'
header-img: 'img/post-bg-question.jpg'
header-img-credit: ''
header-mask: 0.4
tags:
  - JavaScript
  - Array
  - question
---

# ['1', '2', '3'].map(parseInt)

## 文档

- [Array.map() — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
- [parseInt — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

## 看题

```js
console.log(['1', '2', '3'].map(parseInt))
// -> [1, NaN, NaN] 惊不惊喜？意不意外？😝
```

为什么不是`[1, 2, 3]`?？

## 解析

参考：[why-does-parseint-yield-nan-with-arraymap — stackoverflow](https://stackoverflow.com/questions/262427/why-does-parseint-yield-nan-with-arraymap)

根据`Array.map`的文档，`map`传入的`callback`函数，可以接收 3 个参数：`element,index, array`。

而`parseInt`，根据文档，可以接收 2 个参数：`string，radix`(基数，一个介于 2 和 36 之间的整数。默认值为 10，表示使用十进制数值系统)。

那么实际上调用`parseInt`时，是传入 2 个参数`(element, index)`，即：

```js
parseInt('1', 0) // radix为0，且string不以'0'/'0x'开头，使用默认基数10。(具体看下图👇)
parseInt('2', 1) // Fail，1不是有效的基数，基数范围是2~36之间的整数。
parseInt('3', 2) // Fail，'0'或'1'组成的才是有效的二进制数字。
```

> 在基数为 undefined，或者基数为 0 或者没有指定的情况下，JavaScript 作如下处理：

> - 如果字符串 string 以"0x"或者"0X"开头, 则基数是 16 (16 进制).
>
> - 如果字符串 string 以"0"开头, 基数是 8（八进制）或者 10（十进制），那么具体是哪个基数由实现环境决定。ECMAScript 5 规定使用 10，但是并不是所有的浏览器都遵循这个规定。因此，永远都要明确给出 radix 参数的值。
>
> - 如果字符串 string 以其它任何值开头，则基数是 10 (十进制)。

> 如果第一个字符不能被转换成数字，parseInt 返回 NaN。

因此，结果数组为`[1, NaN, NaN]`。🌝

## 改代码

那要怎么写才能得出`[1,2,3]`? 把`parseInt`的第二个参数`radix`固定为 10（十进制）。

`radix`的默认值不就是 10 吗？能不能干脆不传第二个参数？不太好，ES5 规定是 10，但并不是所有浏览器都遵守，最好都要明确基数。

```js
console.log(['1', '2', '3'].map(element => parseInt(element, 10)))
// -> [1, 2, 3]
```
