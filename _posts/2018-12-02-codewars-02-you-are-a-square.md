---
layout: post
title: "Codewars 02: You're a square"
subtitle: "About Number"
author: "Rushan"
header-img: "img/post-bg-codewars.png"
header-mask: 0.4
tags:
  - JavaScript
  - Codewars
---

## 题目

[You're a square!](https://www.codewars.com/kata/youre-a-square/javascript)

> 给定一个整数 n，判断其是否是平方数。返回布尔值。

## 我的解法

1. 总的思路，n 除以比 n 小的整数，判断商是否等于除数。
2. 处理特殊值，如果 n < 0，返回 false；
3. 如果 n <= 1，返回 true;
4. 如果 n > 1，q = n / i，i 从 2 开始递增，当 q === i，返回 true；当 q < i，返回 false；否则 i 继续递增。

```js
var isSquare = function(n) {
    if (n < 0) return false;
    if (n <= 1) return true;
    for (let i = 2; i <= n; i++) {
        let q = n / i;
        if (q === i) return true;
        if (q < i) return false;
    }
};
```

So complicated… 处理数学型问题，我居然忘了 `Math` 这家伙！

优化:

[Math.sqrt()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/sqrt) 返回一个数的平方根。简单明了。

只有平方根是整数，那就给定的数就是平方数了。

怎么判断是整数？比如，除以1的余数为0；用`Number.isInteger()`判断。

```js
var isSquare = function(n) {
    return Math.sqrt(n) % 1 === 0 ? true : false;
}
```
