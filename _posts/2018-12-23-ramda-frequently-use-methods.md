---
layout: post
title: "Ramda 常用方法"
subtitle: "Ramda: frequently use methods"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - Ramda
---

Ramda，一个超好用超棒的函数式编程第三方库，谁用谁知道🙈，这篇是常用的一些方法。

- [Ramda](http://ramdajs.com/docs/)
- [Ramda(中文)](http://ramda.cn/docs/)

## List

```js
// -- filter 过滤满足条件的 --

var isEven = n => n % 2 === 0;
// 也可以用R.modulo()
// var isEven = n => R.modulo(n, 2)===0
R.filter(isEven, [1,2,3,4]) // => [2,4]
R.filter(isEven, {a:1, b:2, c:3, d:4}) // => {"b":2, "d":4}

// -- reject 过滤不满足条件的 --

var isOdd = n => n % 2 === 1;
R.reject(isOdd, [1,2,3,4]) // => [2,4]
R.reject(isOdd, {a:1, b:2, c:3, d:4}) // => {"b":2, "d":4}

// -- find 返回首个满足条件的元素 --

var list = [{a:1}, {a:2}, {b:2}]
// R.propEq('a', 2) 属性a的值为2的，返回true，否则返回false
R.find(R.propEq('a', 2))(list) // => {"a": 2}
R.find(R.propEq('b', 1))(list) // => undefined

// -- findIndex 返回首个满足条件的元素的索引 --

R.findIndex(R.propEq('a', 2))(list) // => 1
// 不存在满足的元素，返回-1
R.findIndex(R.propEq('b', 1))(list) // => -1 

// -- uniq 列表去重 --

R.uniq([1, 2, 1, '1']) // => [1, 2, "1"]
// 天呐，连有相同属性的对象都能去重！
R.uniq([{a:1}, {a:1}, {b:2}, [13], [13]]) // => [{"a": 1}, {"b": 2}, [13]]

//-- map (function)(functor)将函数应用到functor的每个值上 --

var square = x => x*x
var opposite = x => -x
var arr = [1, 2, 3]
var obj = {a:1, b:2, c:3}

R.map(square)(arr) // => [1, 4, 9]
R.map(square)(obj) // => {a:1, b:4, c:9}

// 文档：Also treats functions as functors and will compose them together.
// 下面comp2 等价于comp1
var comp1 = R.compose(opposite, square)
R.map(comp1)(arr) // => [-1, -4, -9]

var comp2 = R.map(opposite)(square)
R.map(comp2)(arr) // => [-1, -4, -9]
```

## Function

```js
// -- curry 对函数进行柯里化 --

var f = (a, b, c) => a - b - c;
f(3, 2, 1) // => 0

var g = R.curry(f)
// 参数不需要一次只传入一个，以下写法等价
g(3)(2)(1)
g(3, 2)(1)
g(3)(2, 1)

// 占位符R.__可用于标记暂未传入参数的位置。
var _ = R.__
// 以下写法等价
g(_, 2, 1)(3)
g(_, _, 1)(3)(2)
g(_, _, 1)(3, 2)
g(_, 2)(3)(1) // 3传入占位符，1传入最后
g(_, 2)(_, 1)(3) // 第2个占位符传入第1个占位符，1传入最后，3传入占位符

// -- compose 从右往左执行函数组合 --

var classyGreeting = (firstName, lastName) => "The name's " + lastName + "," + firstName + " " + lastName
// 最右侧函数可以是任意元函数（参数个数不限），其余函数必须是一元函数。
// classyGreeting不是一元函数，只能放右边
var yellGreeting = R.compose(R.toUpper, classyGreeting)
// compose 输出的函数不会自动进行柯里化。所以调用函数时，右边函数需要几个参数，就得一次性给几个参数
// 即，不能 yellGreeting('James')('Bond')
yellGreeting('James', 'Bond') // => "THE NAME'S BOND,JAMES BOND"

// 先乘2，再加1，最后求绝对值（absolute value）
R.compose(Math.abs, R.add(1), R.multiply(2))(-4) // => 7 (Math.abs, 绝对值)
```

## Object

```js
// -- clone 深拷贝 --

var obj = [{}, {a: {}}, [2], function(){}];
var objClone = R.clone(obj);
obj === objClone // => false
// 对象，生成副本，而不是引用地址
obj[0] === objClone[0] // => false
obj[1]['a'] === objClone[1]['a'] // => false
// function 通过引用复制
obj[3] === objClone[3] // => true

// -- prop 取出对象中指定属性的值 --

R.prop('a', {a: 10}) // => 10
//如果不存在，则返回undefined
R.prop('a', {b: 10}) // => undefined

// -- propEq "Eq: equal"取出指定对象属性与给定的值相等，返回Boolean --

var abby = {name: 'Abby', age: 7, hair: 'blond'}
var fred = {name: 'Fred', age: 12, hair: 'brown'}
var rusty = {name: 'Rusty', age: 10, hair: 'brown'}
var alois = {name: 'Alois', age: 15, disposition: 'surly'}
var kids = [abby, fred, rusty, alois]

var hasBrownHair = R.propEq('hair','brown')
// 筛选出有brown hair的kids信息
R.filter(hasBrownHair, kids) // => [{"age": 12, "hair": "brown", "name": "Fred"}, {"age": 10, "hair": "brown", "name": "Rusty"}]
// 筛选出有brown hair的kids名单
R.map(R.prop('name'), R.filter(hasBrownHair, kids)) // => ["Fred", "Rusty"]

// 要多个属性相同，用whereEq
var blondHairSeven = R.whereEq({hair: 'blond', age: 7})
R.map(R.prop('name'), R.filter(blondHairSeven, kids)) // => ["Abby"]

```