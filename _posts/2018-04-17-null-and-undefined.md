---
layout: post
title: "null还是undefined"
subtitle: "null and undefined"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
---

返回值是null还是undefined，这都不重要。

```js
function doBlankFunction {
  return null;
}

funcion doBlankFunction2 {
  
}

if (doBlankFunction()) {
} else {
}

if (doBlankFunction2()) {
} else {
}
```

null和undefined都是“假”值，我只要判断是否是“真”即可。

真正需要追问的是为什么null和undefined是“假”值。这个知识点值得去深入研究。

## 区别

`null` 表示一个值被定义了，定义为“空值”；
`undefined` 表示根本不存在定义。

所以设置一个值为 `null` 是合理的，如
`objA.valueA = null;`
但设置一个值为 `undefined` 是不合理的.

### `null`典型用法

1. 作为函数的参数，表示该函数的参数不是对象。

2. 作为对象原型链的终点。

```js
Object.getPrototypeOf(Object.prototype)
// null
```

### `undefined`典型用法

1. 变量被声明了，但没有赋值时，就等于undefined。

2. 调用函数时，应该提供的参数没有提供，该参数等于undefined。

3. 对象没有赋值的属性，该属性的值为undefined。

4. 函数没有返回值时，默认返回undefined。

```js
var i;
i // undefined

function f(x){console.log(x)}
f() // undefined

var  o = new Object();
o.p // undefined

var x = f();
x // undefined

```

## 参考资料

1. [关于typeof当中的Undefined和Null的区别 — github](https://github.com/xugy0926/getting-started-with-javascript/issues/177)
2. [undefined与null的区别 — 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html)