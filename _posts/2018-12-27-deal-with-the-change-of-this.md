---
layout: post
title: "如何应对`this`变化"
subtitle: "Deal with the change of `this`"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - this
---

## 引子

先看看下面的代码，sample 1 输出什么？

```js
// sample 1
var obj = {
    setData: function(value) {
        console.log(value);
    },
    fun: function() {
        this.setData(1);
    }
};

obj.fun();
```

sample 2 输出什么？执行的结果与你认为的结果相同吗？

```js
// sample 2
var obj = {
    setData: function(value) {
        console.log(value);
    },
    fun: function() {
        setTimeout(function() {
            this.setData(1);
        }, 100);
    }
};

obj.fun();
```

这段代码，看起来的意图是希望`this.setData(1)`是调用`obj`的`setData`方法。但实际上的结果事与愿违。

结果是`TypeError`：`Uncaught TypeError: this.setData is not a function`

因为执行异步回调函数时，里面的`this`指向的是全局对象，而全局对象没有`setData`方法，所以报错。（每个函数都是独立存在的，可以运行在不同的环境。函数内部的`this`，指向函数当前的运行环境。）

注：不是很明白的，可以参考我的另一篇文章 [JavaScript 的 this、闭包](/2018/11/18/JavaScript-this-and-closure/)。

那怎么办？异步情况下，怎么能调用到`setData`方法?

## 3种方案

1. 用`that`保存`this`
2. 用`bind`主动绑定
3. 用箭头函数

### 用`that`保存`this`

```js
// sample 3-1
var obj = {
    setData: function(value) {
        console.log(value);
    },
    fun: function() {
        const that = this;
        setTimeout(function() {
            that.setData(1);
        }, 100);
    }
};

obj.fun();
```

原理，闭包。用`that`保存`this`(指向`obj`)，闭包使得局部变量`that`不被释放。

由于回调函数内部仍用到`that`，所以，即使异步回调函数执行时`fun`函数已经`return`，但`that`没有被销毁，回调函数仍然可以访问到。

### 用`bind`主动绑定

```js
// sample 3-2
var obj = {
    setData: function(value) {
        console.log(value);
    },
    fun: function() {
        setTimeout(function() {
            this.setData(1);
        }.bind(this), 100);
    }
};

obj.fun();
```

关于[bind(thisArg) — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)。

> The bind() method creates a new function that, when called, has its this keyword set to the provided value.

使用`bind`方法，函数会创建一个新绑定函数，绑定函数与被调函数具有相同的函数体。调用绑定函数时，函数的`this`被指定为给定的值。

### 用箭头函数

```js
// sample 3-3
var obj = {
    setData: function(value) {
        console.log(value);
    },
    fun: function() {
        setTimeout(() => {
            this.setData(1);
        }, 100);
    }
};

obj.fun();
```

关于[箭头函数 — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。

箭头函数没有自己的`this`，它只会从自己的作用域链的上一层继承`this`。

所以，sample 3-3 中，箭头函数里的`this`，是继承上一层`fun`函数的`this`。`fun`函数由`obj`调用，`this`指向`obj`。

## More

回到 sample 1 看一个变体：

```js
// sample 1-2
var obj = {
    setData: function(value) {
        console.log(value);
    },
    fun: function() {
        this.setData(1);
    }
};

// 方式1
// obj.fun();

// 方式2
const fun = obj.fun;
fun();
```

这次输出什么？

`obj.fun`是函数的地址，把地址给了全局变量`fun`，那么`fun()`调用函数，函数的运行环境是？是全局对象。全局对象没有`setData`方法，所以，结果是`TypeError`：`Uncaught TypeError: this.setData is not a function`。