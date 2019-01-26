---
layout: post
title: "nodejs的2种执行方式"
subtitle: "2 way of executing nodejs code"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
---

## 终端REPL运行环境

Node.js REPL(Read Eval Print Loop:交互式解释器)，这个环境可以用于简单的小代码验证。

直接输入`node`进入交互模式，相当于启动了Node解释器。

等待你一行一行地输入源代码，每输入一行就执行一行。

如果要执行多行代码，那就输入`.editor`进入编辑器模式，然后输入要执行的多行代码，`ctrl+D`结束输入，就能输出执行结果。

![.editor](https://ws2.sinaimg.cn/large/006tNbRwgy1fx648wil16j30vo0majuj.jpg)

REPL还有其他的命令，可以输入`.help`查看：

![.help](https://ws4.sinaimg.cn/large/006tNbRwgy1fx649vk7vij30vo092wg6.jpg)

## Node.js运行环境

这就是正在执行代码的环境。

直接运行`node hello.js`文件相当于启动了Node解释器，然后一次性把`hello.js`文件的源代码给执行了，你是没有机会以交互的方式输入源代码的。

## 参考资料

1. [运行node… — github](https://github.com/xugy0926/getting-started-with-javascript/issues/335)
2. [第一个Node程序 — 廖雪峰](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434501436552e03ec6cc152b4c84959f14d0ea278488000)