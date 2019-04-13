---
layout: post
title: "【小题一道】this、IIFE"
subtitle: "Question about this，IIFE "
author: "Rushan"
header-img: "img/post-bg-question.jpg"
header-img-credit: ""
header-mask: 0.4
tags:
  - JavaScript
  - IIFE
  - this
---

下面代码会在 console 输出什么？为什么？

```js
var myObject = {
    foo: 'bar',
    func: function () {
        var self = this;
        console.log('outer func: this.foo = ' + this.foo);
        console.log('outer func: self.foo = ' + self.foo);
        (function () {
            console.log('inner func: this.foo = ' + this.foo);
            console.log('inner func: self.foo = ' + self.foo);
        }());
    }
};
myObject.func();

```

输出是：

```js
// outer func: this.foo = bar
// outer func: self.foo = bar
// inner func: this.foo = undefined
// inner func: self.foo = bar
```

## 复习 & 分析

👉 复习下`this`:

> 在函数内部，`this`的值取决于函数被调用的方式。—— [this — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)

> `this`设计目的就是在函数体内部，指代函数当前的运行环境。—— [JavaScript的this原理 — 阮一峰](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)

👉 再复习下IIFE(立即执行函数表达式):

- [Immediately-Invoked Function Expression (IIFE) — Ben Alman ](http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife)
- [IIFE 立即执行函数表达式](/rushan-blog/2018/11/20/IIFE/)

😜 也可以先看分析再回头复习，哈哈~

- 第一个log，`func`是作为`myObject`的方法被调用的，`this`指向对象`myObject`。所以`this.foo`相当于`myObject.foo`。
- 第二个log，出现`self`，先在当前作用域找，找到了，值是`this`，而`this`又指向`myObject`。所以，同上。
- 第三个log，是不是有点小困惑？那就先放着，等下面试验。
- 第四个log，出现`self`，当前函数没有，往上到`func`找，哈哈，找到了~ 所以，同第二个log。

## 为什么是`undefined`

第三个log是`undefined`，为啥？这里的`this`是何许人也？打log！

```js
// 在IIFE里加log
        (function () {
            console.log(this === window); // 浏览器运行环境
            // console.log(this === global); // node运行环境
        }());
// -> true
```

当然，你也可以用`console.log(this);`直接打印`this`。就是输出会很长。

根据`log`可以看出，`this`指向全局对象，全局对象没有`foo`属性，因此前面第三个log是`undefined`。

### `this`为什么是指向全局对象？

查看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this):在函数内部，`this`的值取决于函数被调用的方式。在非严格模式下，简单调用情况下，如果`this`的值没有被`call`等方法设定，`this`的值默认指向全局对象。

这里的IIFE不是被`myObject`调用的，所以`this`不是指向`myObject`；也没有设置`this`的值，所以`this`默认指向全局对象。
