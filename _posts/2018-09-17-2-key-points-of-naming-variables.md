---
layout: post
title: "变量命名的2个要点"
subtitle: "2 key points of naming variables"
author: "xugaoyang"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - 变量命名
---

引自[变量命名简明技巧 - JavaScript社区](https://xugaoyang.com/post/S3lz9seDu9)

1. 完整代表了变量的含义吗？ -> 可推导出更多信息（如，变量数据类型、变量含义）
2. 以问题为导向了吗？ -> 要what不要how，反映了问题而非解决方案

```js
var start = 0; // bad。 开始什么？不明白
var startCount = 1; // good。  可以推导出是number类型

var end = 100; // bad。 end什么？不明白
var endCount = 100; // good。可以推导出是结束数字，number类型。

var file = './name_style.js'; // bad。 文件内容？不清楚
var jsFilePath = './name_style.js'; // good。可以推导出是js文件的路径，路径一定是字符串。

var id = '1234567'; // bad 什么id？不明白
var userId = '1234567'; // good。可以推导出是用户的id。

var weight = 60; // bad 重量单位是什么？
var weightInKg = 60; // good 重量60kg

// 描述一个人的基本信息

var person = {
  // bad。person可以表示一个人，但没办法知道是个人信息
  name: 'xiaoming',
  age: 18,
  sex: 'man'
};

var personInfoObj = {
  // good。可以推导出这是个人信息，而且还是一个object类型。
  name: 'xiaoming',
  age: 18,
  sex: 'man'
};

// 表示一个输入的雇员信息

var inputData = {
  // bad。只能表示是输入的数据。输入的什么数据？不清楚。只知道how，不知道what。
  name: 'xiaoming',
  age: 18,
  sex: 'man'
};

var employeeData = {
  // good。 这是个雇员的数据(很明确知道what是什么)，很清晰。
  name: 'xiaoming',
  age: 18,
  sex: 'man'
};

```