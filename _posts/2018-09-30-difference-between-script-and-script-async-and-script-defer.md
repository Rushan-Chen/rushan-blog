---
layout: post
title: "script、script async和script defer的区别"
subtitle: "Difference between script,script async and script defer"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - 浏览器

---

`<script>` `<script async>` `<script defer>` 3种标签，浏览器解析它们的时候顺序不一样。

* `<script>` 
  - HTML 解析中断，脚本被提取并立即执行。执行结束后，HTML 解析继续。
  
    ![script](https://ws2.sinaimg.cn/large/006tNbRwgy1fwa40tves6j30lo048jre.jpg)

* `<script async>` 
  - 脚本的提取、执行的过程与 HTML 解析过程并行，脚本执行完毕可能在 HTML 解析完毕之前。当脚本与页面上其他脚本独立时，可以使用`async`，比如用作页面统计分析。

    ![script async](https://ws3.sinaimg.cn/large/006tNbRwgy1fwa414i3n2j30lo0480so.jpg)

* `<script defer>` 
  - 脚本仅提取过程与 HTML 解析过程并行，脚本的执行将在 HTML 解析完毕后进行。如果有多个含`defer`的脚本，脚本的执行顺序将按照在 document 中出现的位置，从上到下顺序执行。

    ![script defer](https://ws1.sinaimg.cn/large/006tNbRwgy1fwa41ka5btj30lo048jrb.jpg)

    photos via [Asynchronous vs Deferred JavaScript](https://bitsofco.de/async-vs-defer/)

注意：没有`src`属性的脚本，`async`和`defer`属性会被忽略。
