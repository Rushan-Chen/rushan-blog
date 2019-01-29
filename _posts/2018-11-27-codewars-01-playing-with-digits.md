---
layout: post
title: "Codewars 01: Playing with digits"
subtitle: "About Number"
author: "Rushan"
header-img: "img/post-bg-codewars.png"
header-mask: 0.4
tags:
  - JavaScript
  - Codewars
---

## 题目

[Playing with digits](https://www.codewars.com/kata/playing-with-digits/javascript)

> 是否存在整数 k，使得 (a ^ p + b ^ (p+1) + c ^(p+2) + d ^ (p+3) + ...) = n \* k
>
> 如果存在 return k， 否则 return -1。
>
> 注: 给定的 n, p 都会是严格正整数。

## 我的解法

思路：

1. 为了拿到`n`的各位数字，将其转换为数组
2. 遍历数组，计算等式左边的总和 (a ^ p + b ^ (p+1) + c ^(p+2) + d ^ (p+3) + ...)
3. 判断总和能否整除`n`，能则返回商，否则返回-1

```js
// 第一版
function digPow (n, k) {
    let nArray = n.toString().split('').map(v => +v);
    let leftSum = 0;
    nArray.forEach((num, index) => leftSum += Math.pow(num, p + index));
    leftSum % n === 0 ? leftSum / n : -1;
}
```

可优化的：

- `n.toString()` 可简化为`String(n)`。
- `.map(v => +v)` 把数组里的字符串元素转换为 Number 类型，这一步可省略。因为`Math.pow(base, exponent)`有容错机制，内部会把`base`转换为 Number 类型。
- `forEach` 换成 `reduce`，两者的比较见：[数组 forEach VS reduce](https://github.com/Rushan-Chen/JavaScript/blob/master/note/17-array-reduce.md)。`reduce` 方法可以设置累加值及其初始值，且返回值就是累计值。
- 对`leftSum % n === 0` 的判断可以简化为对`leftSum % n`的判断

```js
// 第二版
function digPow(n, p){
    var nArray = String(n).split('');
    var sum = nArray.reduce((sum, num, index) => sum += Math.pow(num, p+index), 0);
    return sum % n ? -1 : sum / n;
}
```
