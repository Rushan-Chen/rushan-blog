---
layout: post
title: "Ramda math相关方法"
subtitle: "About "
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - Ramda
---

继上篇[Ramda 常用方法](/2018/12/05/ramda-frequently-use-methods/)后，这篇是一些数学方面的一些方法。

```js
const R = require('ramda');

// Ramda math practice

// -- add 加 --
R.add(1,2) //=> 3
R.add(1)(3) //=> 4

// -- subtract 减 --
R.subtract(13,7) //=> 6

var minus5 = R.subtract(R.__, 5);
minus5(17) //=>12

// 余角
var complementaryAngle = R.subtract(90);
complementaryAngle(30) //=> 60
complementaryAngle(45) //=> 45

// -- dec 减1 --
R.dec(13) //=> 12

// -- inc 加1 --
R.inc(11) //=> 12

// -- divide 除 --
R.divide(13, 10)  //=> 1.3

var half=R.divide(R.__, 2)
half(24)  //=> 12

// 倒数
var reciprocal = R.divide(1)
reciprocal(4)  //=> 0.25

// -- multiply 乘 --
var double = R.multiply(2)
var triple = R.multiply(3)
double(3) //=> 6
triple(7) //=> 32
R.multiply(2, 5)

// -- mean 平均值 --

R.mean([3, 7, 13, 17, 37, 73])  //=> 25
R.mean([]) //=> NaN

// -- median 中位数 --
R.median([1,3,7])  //=> 3
R.median([1,8,3,11]) //=> 5.5 (3和8的均值)

// -- modulo 余数（JS）--
R.modulo(17,3) //=> 2
// JS behavior:
R.modulo(-17,3) //=> -2
R.modulo(17, -3) //=> 2

// 奇数
var isOdd = R.modulo(R.__, 2)
isOdd(13) //=> 1 (真，是奇数)
isOdd(6) //=> 0 (假，是偶数)

// -- sum 数组元素求和 --
R.sum([2,4,6,8,100,1]) //=> 121
R.sum(['H','e','l','l','o',',','J','S']) //=> NaN

// -- product 数组元素相乘 --
R.product([2,4,6,8,100,'1']) //=> 38400

```