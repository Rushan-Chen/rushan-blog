---
layout: post
title: "JavaScript的this、闭包"
subtitle: "About this and closure"
author: "Rushan"
header-img: "img/post-bg-default.jpg"
header-img-credit: "Photo by Andy Holmes on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/LUpDjlJv4_c"
header-mask: 0.4
tags:
  - JavaScript
  - 闭包
  - this
---

由[闭包 — JavaScript社区](https://xugaoyang.com/post/7jqi00qvjf)延伸。

分析代码前，先了解一下`this`是啥。

## 函数的`this`关键字

### 1. `this`的由来

参考 [JavaScript 的 this 原理 — 阮一峰](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html) 的文章，`this`的由来， 与内存里的数据结构有关，引擎会将函数单独保存在内存中，由于函数是一个单独的值，所以它可以在不同的环境（上下文）执行（比如 object 中、全局环境中）。

当函数体里面使用了（不在函数体内定义的）变量，该变量由运行环境提供，就需要有一种机制，能够在函数体内部获得当前的运行环境（context）。所以，`this`就出现了，它的设计目的就是 **在函数体内部，指代函数当前的运行环境**。

> `this`设计目的就是在函数体内部，指代函数当前的运行环境。

### 2. strict、nonstrict 模式，node、浏览器环境下 this 的区别

```js
// --- 非严格模式 ---
function thisNonstrict() {
  return this;
}
console.log(thisNonstrict() === global); // node环境，true
// console.log(thisNonstrict() === window); // 浏览器环境，true

// --- 严格模式 ---
function thisStrict() {
  "use strict";
  return this;
}
console.log(thisStrict() === undefined); // node环境，true
// console.log(thisStrict() === undefined); // 浏览器环境，true
// console.log(window.thisStrict() === window); // 浏览器环境，true

// --- 全局对象与全局变量 ---
var name = "The Window";
console.log(this.name);
// -> undefined  node环境，name不是全局对象global的属性
// -> "The Window"  浏览器环境，name是全局对象window的属性
```

## 理解 sample 代码

> 理解以下两个片段代码的运行机制

### 1. sample1

```js
// sample1
var name = "The Window";
var object = {
  name: "My Object",
  getNameFunc: function() {
    return function() {
      return this.name;
    };
  }
};

console.log(object.getNameFunc()());
```

要打印`object.getNameFunc()()`，先是找`object.getNameFunc`，是对象的方法，用`()`调用该方法，会`return`一个`function`，跳出对象的方法。假如给它个名字`fun`，那就相当于：

```js
var name = "The Window";
function fun() {
  return this.name;
}
console.log(fun());
```

非严格模式下，`this`就是在函数体内部，指代函数当前的运行环境。函数`fun` 当前运行在全局环境，指向全局对象。

前面说过，在 node 环境下，全局变量不是全局对象的属性，所以在 node 环境下运行时，会返回`undefined`；浏览器环境下，全局变量是全局对象的属性，所以在浏览器环境下运行时，会返回全局变量`name`的值`"The Window"`。

Q: ...`this`不是指向`object`？不要骗我，我写的代码少。

那就打 log 吧 ~

```js
// sample1 + console.log
var name = "The Window";
var object = {
  name: "My Object",
  getNameFunc: function() {
    console.log(this === object); // true 对象方法的this是object
    return function() {
      console.log(this === global); // 回调函数的this指向全局对象。node环境，global，true；浏览器环境，window，true
      return this.name; // node环境，undefined; 浏览器环境，"The Window"
    };
  }
};
```

Q: 为什么是指向全局对象，它不是也在`object`里吗？

看起来在，不代表实际执行时也在嘛。根据阮一峰说的数据结构，变量`object`存的是 **对象的地址**，引擎先从`object`拿到内存地址，然后再从该地址读出原始的对象，读取`getNameFunc`里存的 **函数的地址**，调用该函数。`object.getNameFunc()`是通过`object`找到`getNameFunc`，所以是在`object`环境执行。

调用后，函数`return`了一个值 ，那这个函数就结束了（不在 stack 里了），接下来就不再是在`object`环境执行了，而是在全局环境。

模仿着画了个图，不知道对不对，红色虚框表示在`object`环境。

<img src="https://ws4.sinaimg.cn/large/006tNc79gy1ftprngwlizj30u0183ae1.jpg" width="400px">

### 2. sample2

```js
// sample2
var name = "The Window";
var object = {
  name: "My Object",
  getNameFunc: function() {
    var that = this;
    return function() {
      return that.name;
    };
  }
};

console.log(object.getNameFunc()());
```

同上一个sample，要打印`object.getNameFunc()()`，先是`object.getNameFunc()`，调用对象的`getNameFunc`方法，会`return`一个函数，定义这个函数的时候，发现里有个`that.name`，emmm… 是谁？

不是当前函数自己定义的，沿着作用域链，到上级函数找，发现`that`变量，值是`this`。上级函数在`object`环境执行，`this`指向`object`。那么，`that.name`就相当于`object.name`，也就是`"My Object"`! 嗯，定义函数的时候，把找到的`that`值放进“包包”（闭包）。等被调用 ~

Q:emmm… 不是说`return`就不在`object`环境执行了吗？怎么还能拿到`object.name`的值？？

定义的时候就把值放进“包包” 背走了嘛，好吧，只是形象的说法，正经地说，不，正经地引用徐帅的解释：

> 闭包的特点就是能把原始作用域的内容带出去（事实上是同作用域下的变量可以访问），这里要重点记住一个特点：如果在某个作用域下但凡还有一个变量（包括函数变量）还在使用，那么这个作用域下的所有其他变量都不会销毁。
> 闭包保证局部变量不被释放。

原始作用域下的`name`还在被 return 的函数使用，那就不会被销毁。

## 参考资料

- [JavaScript 的 this 原理 — 阮一峰](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)

- [闭包函数认知](http://xugaoyang.com/post/59d724375706b322621783bc)
