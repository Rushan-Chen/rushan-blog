---
layout: post
title: "Codewars 03: Your order, please"
subtitle: "About string & array"
author: "Rushan"
header-img: "img/post-bg-codewars.png"
header-mask: 0.4
tags:
  - JavaScript
  - Codewars
---

## 题目

[Your order, please](https://www.codewars.com/kata/your-order-please)

给定字符串里每个单词里都有一个数字（1~9），实现输出的新字符串里的单词按照数字从小到达排序。

例子：

> "is2 Thi1s T4est 3a"  -->  "Thi1s is2 3a T4est"
> 
> "4of Fo1r pe6ople g3ood th5e the2"  -->  "Fo1r the2 g3ood 4of th5e pe6ople"
> 
> ""  -->  ""

思路：

- 若为空字符串，返回`''`
- 非空字符串转换为数组，遍历数组，找到单词里的数字
- 将数字作为索引，单词作为元素，存在新数组里（数组根据索引自动排序）
- 将新数组连为字符串，去除左边开头由于空元素造成的空格，返回字符串

```js
// 第一版
function order (words) {
    if (!words) return '';
  
    let arr = [];
    words.split(' ').map(word => {
        let num = word.match(/\d/).toString();
        arr[num] = word;
    });
    return arr.join(' ').trimLeft();
}
```

最高票数的版本，思路：

- 字符串转换为数组
- 数组用sort方法进行排序，传入一个函数，比较两两单词中的数字的大小
- 排序后的数组重新连成字符串

```js
function order (words) {
    return words.split(' ').sort(
        (a, b) => a.match(/\d/) - b.match(/\d/)
    ).join(' ');
}
```

反思：

对`array.sort()`的掌握不够熟悉。`string.match()`返回的是含一个元素的数组，数组相减会进行隐性转换，转换为数字再相减。sort里的函数`compareFunction(a, b)`，返回值为负，则a会排在b前面；返回值为正，则a会排在b后面。