---
layout: post
title: "Codewars 03: Palindrome for your Dome"
subtitle: "About String"
author: "Rushan"
header-img: "img/post-bg-codewars.png"
header-mask: 0.4
tags:
  - JavaScript
  - Codewars
---

## 题目

[Palindrome for your Dome](https://www.codewars.com/kata/palindrome-for-your-dome/javascript)

判断给定的字符串是不是回文字符串（正着读反着读都一样）。

注：不能使用`reverse()`方法；空字符串`""`在本题也是回文字符串。

## 解法

思路：

1. 清理字符串，只留下字母，并统一大小写；
2. 将清理后的字符串反过来：
    1. 将字符串转为数组；
    2. 让数组元素从右往左相加得到新字符串。
3. 将但字符串与清理后的字符串比较，看是否相等。

```js
function palindrome(string) {
  const cleanStr = string.replace(/[^A-Za-z]/g, '').toLowerCase();
  return cleanStr === cleanStr.split('').reduceRight((sum, v) => sum + v);
}
```