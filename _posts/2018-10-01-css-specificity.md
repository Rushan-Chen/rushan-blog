---
layout: post
title: "CSS Specificity"
subtitle: ""
author: "Rushan"
header-img: "img/post-bg-css-logo.jpg"
header-img-credit: "Photo by Brandon Morelli"
header-img-credit-href: "https://codeburst.io/master-css-learn-css3-css4-flexbox-animations-grids-frameworks-and-more-dbf69ac4ff61"
header-mask: 0.4
tags:
  - CSS
---

一个CSS选择题：

![CSS](https://ws2.sinaimg.cn/large/006tNbRwgy1fv34zt47y2j31kw0zle21.jpg)

想啥呢，试代码呀~

![blue](https://ws3.sinaimg.cn/large/006tNbRwgy1fv31t5lyawj31eo0iswhx.jpg)

咦，都是blue。

问题来了，和HTML里写的顺序无关，那和什么有关？只管大胆尝试呀~

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fv31uvub5cj31eu0i4goq.jpg)

调整了CSS里选择器的顺序，变成了red。

🌝 有点意思吧~ 所以是为什么？来探究下~

主要参考：
- [Cascade_and_inheritance — MDN](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Introduction_to_CSS/Cascade_and_inheritance#)
- [Specificity — MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Specificity)
- [Specifics on CSS Specificity — CSS-TRICKS](https://css-tricks.com/specifics-on-css-specificity/)

CSS（Cascading Style Sheets）层叠样式表，样式一层层，那怎么决定最后是应用哪个样式？要有重要性（weight）之分。

## 重要性**递减** ⬇️ 

- 内联样式表（Inline styles）
    ```html
        <p style="color: red; margin-left: 20px">
            This is a paragraph
        </p>
    ```

- 内部样式表（在`<head>`里的`<style>`标签内定义）
    ```html
    <head>
        <style type="text/css">
            body {background-color: red}
            p {margin-left: 20px}
        </style>
    </head>
    ```
- 外部样式表
    ```html
    <head>
        <link rel="stylesheet" type="text/css" href="mystyle.css">
    </head>
    ```
    - !Important（🌚 切勿滥用⚠️）
  
        此声明将覆盖任何其他声明。
        (这是一个开挂的家伙，它也可以覆盖最上面的内联样式，放在这里是因为一般是写在外部样式表里)

    - 特异性（Specificity）（可量化比较，见下文👇）
        - ID
            - ID选择器（eg. `#example`）
        - 类级别（Class level）
            - 类选择器（class selectors） (eg. `.example`)
            - 属性选择器（attributes selectors）（eg. `[type="radio"]`）
            - 伪类（pseudo-classes）（eg. `:hover`）
        - 元素级别（Element level）
            - 类型选择器（type selectors）（eg. `h1`）
            - 伪元素（pseudo-elements）（eg. `::before`）
    - 源代码次序（Source order）

        多个CSS声明中，当**Specificity相等**，CSS中**最后**的那个声明将会被**应用**到元素上。

## 特异性（Specificity）的量化

衡量选择器的特异性（Specificity）

注：以下量化的得分表示形式`a,b,c,d`(其中`b`、`c`、`d`是该种类选择器的个数)，看似数学里的十进制数值，但并不是，各位数字是单独比较的。


- a,0,0,0：(特殊的)内联样式（Inline styles）

- 0,b,0,0：ID
    - ID选择器
- 0,0,c,0：类级别
    - 类选择器
    - 属性选择器
    - 伪类
- 0,0,0,d：元素级别 
    - 类型选择器
    - 伪元素
  
内联样式比较特殊，不是外部样式表的，这种声明没有选择器。如果使用了inline style，那么`a = 1`,Specificity为1,0,0,0；如果没有使用内联样式，则`a = 0`。

`a、b、c、d`权重从左到右，依次减小。判断优先级时，从左到右，一一比较，直到比较出最大值，即可停止。所以，如果b的值不同，那么c和d不管多大，都不会对结果产生影响。比如0,1,0,0的优先级高于0,0,11,11。

🌰 1：

```css
ul #nav li.active a
```
- ID，`#nav`，b = 1
- 类级别，`.active`，c = 1
- 元素级别，`ul li a`，d = 3

Specificity为 0,1,1,3

🌰 2：

```html
<li style="color:red;">
```

使用inline style，a = 1，Specificity为 1,0,0,0。（只有`!important`能覆盖它）

Specificity越高分(过度特异)，之后想加新样式，要写出比之前更高Specificity的…

通常，css文件中，**通用的样式放上面，特异性越高的越放下面**，但还是尽量低特异性。
  
## 开头的问题

开头那个选择题，其实就是Specificity相等，于是考虑源代码次序（Source order）：blue放最后，就应用blue；red放最后，就应用red。


【Read More】

- [CSS Specificity Explained By Hopelessly Shopping for New Clothes — Medium](https://medium.freecodecamp.org/css-specificity-explained-by-hopelessly-shopping-for-new-clothes-92bade5f2e5b) 解释Specificity，用买衣服来类比，挺有趣的，可以看看~

- [Understanding CSS: Selector Specificity — Medium](https://medium.com/@dte/understanding-css-selector-specificity-a02238a02a59)