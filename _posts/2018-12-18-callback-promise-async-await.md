---
layout: post
title: '关于callback、Promise和async/await'
subtitle: 'About allback,Promise and async/await'
author: 'Rushan'
header-img: 'img/post-bg-universe.jpg'
header-img-credit: ''
header-img-credit-href: ''
header-mask: 0.4
tags:
  - JavaScript
  - 异步
  - Promise
  - async/await
---

注：本文是主要是整理记录了[xugaoyang](https://github.com/xugy0926)的讲解。

## 回调函数 深度嵌套

### 引子

下面 sample 1-1 的输出结果是什么？

```js
// sample 1-1
function A(fn) {
  fn()
}

A(function B() {
  console.log('world')
})

console.log('Hello')
```

那 sample 1-2 的输出结果是什么？

```js
// sample 1-2
function A(fn) {
  setTimeout(fn, 1000)
}

A(function B() {
  console.log('world')
})

console.log('Hello')
```

很简单，sample 1-1 是 `world hello`，sample 1-2 是 `hello world`（实际输出有换行）。

把代码分割，上面是 `A` 函数的实现，下面是对 `A` 的使用。

如果站在使用者角度，使用者完全不知道 `A` 的实现，`A` 今天可能是同步，明天可能是异步。使用者和实现者之间产生了一个问题 —— “信任问题”。

基于这个信任问题考虑，还可以出现很多信任问题。

```js
// sample 2
function A(fn) {
  fn()
  fn()
}

// 分割线

A(function B() {
  console.log('world')
})

console.log('Hello')
```

sample 2 的信任问题是，实现者调了 2 次。

```js
// sample 3-1
function joint(str1, str2) {
  consolr.log(str1 + str2)
}

joint('hello', 'world')
joint('hello')
joint()
```

sample 3，会出现参数的信任问题。`joint` 默认信任调用者都传入字符串，但实际情况事与愿违。当然，很快就能想到加上默认值。

```js
// sample 3-2
function joint(str1, str2) {
  consolr.log(str1 ? str1 : '' + str2 ? str2 : '')
}

joint('hello', 'world')
joint('hello')
joint()
```

前面的例子，一个关于回调，一个关于函数的参数处理。如果将两者结合到深度嵌套问题上。

```js
// sample 4
function A(fn) {
  fn('hello', 'world')
}

A(function joint(str1, str2) {
  console.log(str1 + str2)
})
```

这样就出现了 2 个信任问题：

- 回调函数的执行到底是异步还是同步？ 可靠吗？
- 参数的回传到底满足吗？

如果再考虑其他的，比如，回调执行次数上能保证就是一次吗？或者能保证真的执行了回调吗？

信任问题变多了，整个事情就很复杂了。🌀

```js
// sample 5
axios.get(url1, function (response) {
    axios.get(url2, function (response) {
        axios.get(url3, function (response) {
            axios.get(url4, function (response) {
                axios.get(url5, function (response) {

                }
            }
        }
    }
})
```

深度嵌套，不好在哪里？每一次都是信任的嵌套，如果外层的嵌套得不到信任，那就走不进内层的嵌套。

为了解决，需要加入大量的代码来保证。

```js
axios.get(url1, function (response) {
    // check
    axios.get(url2, function (response) {
        // check
        axios.get(url3, function (response) {
            // check
            axios.get(url4, function (response) {
                // check
                axios.get(url5, function (response) {
                    // check
                }
            }
        }
    }
})
```

如果加入大量的 check 代码，就带来了很多编程上的问题。比如：变量的相互覆盖。就带来了 bug🐛。

也就是，在解决信任问题时带来了更多的问题。这就是深度嵌套不好的原因。

怎么办？

## Promise & async/await

- Promise，封装异步函数，很好地屏蔽了回调函数，不用采用显性的 callback，解决了异步回调问题。但，解决不了嵌套问题。
- async/await，用来解决异步的嵌套调用。可以像同步一样处理用 promise 封装的异步函数，让异步看起来直观易懂，不像嵌套那样不直观。整个代码像是同步执行一样，但本质还是异步，没有变。（注意，不是为了把异步写成同步）

详情参考：

- [异步处理对比 - 有对比才更清楚技术演进的目的](https://xugaoyang.com/post/59e04f475c5425181ea1e9e0)
- [async await promise try...catch](https://xugaoyang.com/post/59ddc711da515e18113d3c05)
