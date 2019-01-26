---
layout: post
title: "【小题一道】setTimeout"
subtitle: "Question about setTimeout and IIFE"
author: "Rushan"
header-img: "img/post-bg-question.jpg"
header-img-credit: ""
header-mask: 0.4
tags:
  - JavaScript
  - IIFE
  - 闭包
---

老朋友`setTimeout`来做客，那`console`里输出啥呢？

```js
for(var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i);  
    }, 1000);
}
```

输出是：0 1 2 3 4

答对了吗？恭喜👏👏👏

上面是假的输出👻，真的输出是✋✋✋✋✋，5个5。

这有关Event Loop，先去[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Event_loop)看一看。嗯，`setTimeout`里的 callback 会在过了预定时间后进入 Task Queue 排队，等到 stack 空闲了，Queue 里的 callback 再按顺序进 stack 执行。

可以在线看看runtime：[loupe](http://latentflip.com/loupe/?code=CmZvcih2YXIgaSA9IDA7IGkgPCA1OyBpKyspIHsKICAgIGNvbnNvbGUubG9nKCdiZWZvcmU6IGkgPSAnICsgaSk7CiAgICBzZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICAgIGNvbnNvbGUubG9nKGkpOwogICAgfSwgMTAwMCk7Cn0%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D) （点击右上角可以设置动画的delay时长）

在`setTimeout`前加一行`console.log('before: i = ' + i);`，可以更清楚看到顺序。打开console，可以看到这样的log：

![setTimeout](https://ws2.sinaimg.cn/large/006tNbRwgy1fx672powxrj31kw0xhdmo.jpg)

`for`循环先哗啦啦完事，再轮到 callback 进 stack 执行，callback 内没有定义`i`变量，往上找，在全局作用域发现`i`，这时候`i`已经变成5，所以打印出来的都是5。

## 延时为0呢？

```js
for(var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i);  
    }, 0);
}
```

那把延时调到 0 就行了吧？

哇塞，好醒目！

可惜了🤷‍，还是✋✋✋✋✋。为啥？

大帅哥/小姑娘，咱该排队还是要排队的。0延迟，并不保证马上执行，只是说**最快**是0ms之后执行。可以看看[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Zero_delays)怎么说的。

## 怎么才能输出‘0 1 2 3 4’

那就有请背着闭包🎒的`IIFE`童鞋进场(误)💁

```js
for(var i = 0; i < 5; i++) {
    (function(i){
        setTimeout(function() {
        console.log(i);  
    }, 1000);
    }(i));
}
```

为了方便描述，我将外面的匿名函数成为 funA，`setTimeout`里的称为 funB。`i`作为参数传入 funA，成为 funA 里的局部变量。

按理来说，funA 执行了`setTimeout`，让它自己去一边计时，然后`return`，局部变量`i`应该也同时被销毁了，之后 funB 执行时应该访问不到`i`了呀。

但是，funB 定义的时候，有个`i`，它自己的作用域里没有`i`，往上找，在funA里找到了，于是把`i`放进背包🎒(闭包)里，之后执行的时候，就能访问到背包里的`i`了。

背包纯属形象化描述，下面是正经解释：

> 闭包的特点就是能把原始作用域的内容带出去（事实上是同作用域下的变量可以访问），这里要重点记住一个特点：如果在某个作用域下但凡还有一个变量（包括函数变量）还在使用，那么这个作用域下的所有其他变量都不会销毁。 —— [闭包函数认知](https://xugaoyang.com/post/7jqi00qvjf)

`i`还在 funB 里使用，所以即使 funA 返回了，`i`也不会被销毁。
