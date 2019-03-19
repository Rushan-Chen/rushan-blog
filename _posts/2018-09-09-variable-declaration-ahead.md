---
layout: post
title: '变量提前声明的特点'
subtitle: 'Variable declaration ahead'
author: 'Rushan'
header-img: 'img/post-bg-universe.jpg'
header-img-credit: ''
header-img-credit-href: ''
header-mask: 0.4
tags:
  - JavaScript
  - 变量声明
---

由[变量提前声明的特点 — JavaScript 社区](https://xugaoyang.com/post/WeN3x0NjfS) 延伸。

## TypeError

```js
fun1() // TypeError: fun1 is not a function
var fun1 = function() {
  console.log('hello, js')
}
```

> The `TypeError` object represents an error when a value is not of the expected type.
>
> `TypeError`表明一个值不是预期的类型（type）。

> JS 编译器在处理代码分为两个过程：编译期和执行期。

编译第一行代码时，编译期，编译器在作用域内查找变量`fun1`，因为是用`var`声明，所以在作用域顶部进行声明，且默认值为`undefined`，使得它在作用域内都可以被访问到；

（注：hoisting(提升)只是一个隐喻，用来形容在编译期发生的事情）

执行期，声明后，第一行相当于`undefined();`，括号代表调用函数/方法，预期的类型应该是`function`，`undefined`非预期类型，所以编译器抛出错误`TypeError: fun1 is not a function`。

## ReferenceError

```js
fun2() // ReferenceError: fun2 is not defined
let fun2 = function() {
  console.log('hello, js')
}
```

> The `ReferenceError` object represents an error when a non-existent variable is referenced.
>
> `ReferenceError`表明一个不存在的变量被引用。

编译第一行代码，编译器在作用域内查找变量`fun2`，由于是用`let`声明，所以不在编译期进行提前声明(`let fun2`出现（第二行）才声明)；

没找到这个变量（由于还没声明，所以还不存在），也就是代码尝试引用一个不存在的变量，所以是`ReferenceError: fun2 is not defined`

## 参考资料

- [TypeError — MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypeError)
- [ReferenceError — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError)
- ['let' hoisting? — You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS/issues/767)
- [chat log with the editor of the ES6 spec — twitter](https://twitter.com/awbjs/status/434044880180871168)
