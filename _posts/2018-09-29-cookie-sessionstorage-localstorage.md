---
layout: post
title: "cookie、sessionStorage和localStorage的区别"
subtitle: "Difference between cookie,sessionStorage and localStorage"
author: "Rushan"
header-img: "img/post-bg-browsers.jpg"
header-img-credit: "Photo by outdatedbrowser.com"
header-img-credit-href: "http://outdatedbrowser.com/en"
header-mask: 0.6
tags:
  - 浏览器
---

见下表👇

|          | `cookie`   | `sessionStorage` | `localStorage` |
| -------- | ---------- | -------------- | ---------------- |
| 由谁初始化    | 客户端或服务器，服务器可以使用`Set-Cookie`请求头。 | 客户端        | 客户端      |
| 过期时间      | 手动设置    |  当前页面关闭时       | 永不过期  |
| 在当前浏览器会话（browser sessions）中是否保持不变 | 取决于是否设置了过期时间      | 否             | 是               |
| 是否随着每个 HTTP 请求发送给服务器                 | 是，Cookies 会通过`Cookie`请求头，自动发送给服务器 | 否    | 否     |
| 容量（每个域名）  | 4kb        | 5MB        | 5MB           |
| 访问权限   | 任意窗口      | 当前页面窗口       |  任意窗口    |

参考：
- [HTML5 Web Storage](https://tutorial.techaltum.com/local-and-session-storage.html)
- [Cookies - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)