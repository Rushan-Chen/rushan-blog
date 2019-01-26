---
layout: post
title: "初探JSON.parse和JSON.stringify"
subtitle: "About JSON.parse and JSON.stringify"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - JSON
---

## JSON.parse(text[,reviver])

> 解析 JSON 字符串 -> JavaScript 值或对象

`reviver`函数（可选），用以在返回之前对所得到的对象执行变换(操作)。

> `reviver` 函数目的是给开发者一次修改元数据的机会。明确一个修改结果是必要的，如果不修改就原样返回。

Examples：

```js
// Example-1 对象
const userObj = {
  name: "小明",
  mail: "xiaoming@gmail",
  age: 17
};
// 对象 -> JSON字符串
const userObjStr = JSON.stringify(userObj);
console.log(userObjStr); // => {"name":"小明","mail":"xiaoming@gmail"}

// 解析JSON字符串，并用reviver函数处理 -> 新对象
const userObj2 = JSON.parse(userObjStr, (key, value) => {
  if (key === "mail") return undefined; // 筛选掉mail
  return value;
});
console.log(userObj2); // => {name: "小明", age: 17}
```

```js
// Example-2 数组
const fileList = ["XiaoMing.md", "heyu.md", "liXiang.md"];

// 数组 -> JSON字符串
const fileListStr = JSON.stringify(fileList);
console.log(typeof fileListStr + ":" + fileListStr); // => string:["XiaoMing.md","heyu.md","liXiang.md"]

// 解析JSON字符串，并用函数处理 -> 新数组
const fileList2 = JSON.parse(fileListStr, (key, value) => {
  if (typeof value === "string") {
    return (value = value.toLowerCase()); // 将数组元素改小写
  }
  return value;
});
console.log(typeof fileList2 + ":"); // => object:
console.log(fileList2); // => ["xiaoming.md", "heyu.md", "lixiang.md"]
```

## JSON.stringify(value[, replacer [, space]])

> JavaScript 值(对象或者数组) -> JSON 字符串

注：不可枚举的属性会被忽略，比如 function

### `repalcer`参数（可选）

```js
// Example-1.1
// 对象 ->replacer函数处理，替换值 -> JSON字符串
const userObjStr2 = JSON.stringify(userObj, (key, value) => {
  if (key === "mail") return undefined; // 筛选掉mail
  return value;
});
console.log(userObjStr2); // => {"name":"小明","age":17}
```

```js
// Example-1.2
// 对象 ->replacer数组筛选属性 -> JSON字符串
const userObjStr3 = JSON.stringify(userObj, ["name", "age"]);
console.log(userObjStr3); // => {"name":"小明","age":17}
```

### `space`参数（可选），指定缩进用的空白字符串，用于美化输出（pretty-print）

```js
// Example-1.3
// 缩进4个空格
const userObjStr4 = JSON.stringify(userObj, null, 4);
console.log(userObjStr4);

// {
//     "name": "小明",
//     "mail": "xiaoming@gmail",
//     "age": 17
// }
```

## 参考资料

- [JSON.parse() — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)
- [JSON.stringify() — MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
- [JSON.parse() and JSON.stringify()](https://alligator.io/js/json-parse-stringify/)
- [What you didn’t know about JSON.parse](https://abdulapopoola.com/2018/02/26/what-you-didnt-know-about-json-parse/)
