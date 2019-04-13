---
layout: post
title: "Codewars 05: Weight for weight"
subtitle: "About string & array"
author: "Rushan"
header-img: "img/post-bg-codewars.png"
header-mask: 0.4
tags:
  - JavaScript
  - Codewars
---

## 题目

[Weight for weight](https://www.codewars.com/kata/weight-for-weight/javascript)

根据数字的权重（weight）进行排序，权重等于该数字各个位上的数字之和，比如，100 的权重是 1 (1+0+0=1)，99 的权重是 18 (9+9=18)。

如果权重相同，那就把它们视为字符串进行比较，而不是数字。比如，180 和 90 有相同的权重 9，因为 `'180'` 在 `'90'` 前面，所以 180 应排在 90 前面。

例子：

"56 65 74 100 99 68 86 180 90" -> "100 180 90 56 65 74 68 86 99"

注意：

- 数字字符串的开头或结尾可能会有空白符，两个数字之间的间隔也可能多于一个空白符。
- 不要修改输入值
- 输出的结果无格式要求

## 解法

思路：

- 字符串转换为数组
- 数组元素排序：计算元素的`weight`，比较元素的`weight`相对大小，如果`weight`相等，则比较元素本身
- 数组重新拼为字符串，返回新字符串

```js
// 第一版
function orderWeight(string) {
    if (!string) return '';
    const sum = str => str.split('').reduce((sum, val) => (+sum) + (+val));
    return string.match(/\d+/g).sort((a, b) => {
            return sum(a) - sum(b) === 0
            ? (a > b ? 1 : -1)
            : sum(a) - sum(b);
        }).join(' ');
}

orderWeight("103 123 4444 99 2000"); // => "2000 103 123 4444 99"
orderWeight('  56 65 74 100 99 68  86 180 90 '); // => "100 180 90 56 65 74 68 86 99"
orderWeight(''); // => ""
```

改进点：

- `reduce()` 可以加初始累加值为第二个参数，就不用每次计算都算一遍 `(+sum)`
- return时，将string转为array，将 `string.match(/\d+/g)` 改为用`string.split(' ')`
  - 原先用 `string.match(/\d+/g)` 虽然生成的数组不会有空字符串，但有个缺点，就是单string为 `''` 时，返回值是 `null`，为避免出错，需要前面先处理空字符串。
  - 虽然当字符串有多余空格时，数组会有空字符串，但后面排序时，空字符串会排到最前面，不影响数字的排序。题目也不要求输出的结果要去除多余空格，如果要格式统一美观，最后再用 `string.trimLeft()` 去掉左边可能出现的空格即可。
- 把 `sum(a)-sum(b)` 的值存在变量 `diff` 里，减少调用 `sum()` 计算的次数
- 当 weight 相同时，比较两个字符串，可以用 `string.localeCompare(compareString)` 方法
- 把 `sort([compareFunction])` 方法里的比较函数比较长，提取到变量里，可读性较好，更便于维护。

```js
// 第二版
function orderWeight(string) {
    const sum = str => str.split('').reduce(((sum, val) => sum + (+val)), 0);
    const compare = (a, b) => {
            let diff = sum(a) -sum(b);
            return diff === 0
            ? a.localeCompare(b)
            : diff;
        };
    return string.split(' ').sort(compare).join(' ');
}

orderWeight("103 123 4444 99 2000"); // => "2000 103 123 4444 99"
orderWeight('  56 65 74 100 99 68 86 180 90 '); // => "    100 180 90 56 65 74 68 86 99"
orderWeight(''); // => ""
```

[referenceString.localeCompare(compareString) - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)