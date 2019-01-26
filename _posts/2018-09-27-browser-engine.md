---
layout: post
title: "浏览器内核"
subtitle: "About browser engine"
author: "Rushan"
header-img: "img/post-bg-browsers.jpg"
header-img-credit: "Photo by outdatedbrowser.com"
header-img-credit-href: "http://outdatedbrowser.com/en"
header-mask: 0.6
tags:
  - 浏览器
---

浏览器内核分类：

- Blink (Chrome, Opera)
  - 2013年fork自 Webkit。
- Trident (IE)
  - 1997~2015，由微软研发。
- Gecko (Firefox)
  - 1997年至今，由Mozilla研发。
- Webkit (Safari)
  - 2001年fork自 [KHTML engine](https://en.wikipedia.org/wiki/KHTML)。曾经也是 Chrome 的内核，后来 Google fork Webkit到Blink engine，现在独立于 WebKit 开发。
- EdgeHTML (Edge)
  - 2014年fork自 Trident，删除了旧版IE的遗留代码，并使用Web标准和其他现代浏览器的互用性重写了大部分源代码。


内核 | 状态 | 管理者 | 使用
---|---|---|---
Blink | Active | Google | Chrome、Chromium、Opera、360安全浏览器（10.0）等
Trident | 终止 | Microsoft | IE、Microsoft Outlook邮件客户端 等
Gecko | Active | Mozilla | Firefox、Thunderbird邮件客户端
Webkit | Active | Apple | Safari等
EdgeHTML | Active | Microsoft | Edge、UWP apps

## 参考资料

- [Brower engine — wikipedia](https://en.wikipedia.org/wiki/Browser_engine)

- [Comparison_of_browser_engines - Wikipedia](https://en.wikipedia.org/wiki/Comparison_of_browser_engines)