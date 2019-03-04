---
layout: post
title: "Webpack 中配置 Babel"
subtitle: "About Configuration of babel in webpack"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - Webpack
  - Babel
---

想使用 ES6、ES7 等新标准的新特性编写代码，但大部分浏览器还不支持新特性，怎么办？用[Babel](https://babeljs.io/)，它是 JavaScript 编译器，可以帮我们把代码编译成浏览器兼容的代码 👏。

本文以 webpack 4.x 和 babel 7.3 为例。包管理工具使用`yarn`，如果用`npm`，把`yarn add` 改为 `npm install` 即可。

## 基本配置

### babel-loader、 @babel/core 和 @babel/preset-env

安装

```shell
yarn add -D babel-loader @babel/core @babel/preset-env
```

- `babel-loader` 是用 babel 转译（transpile）js 代码的 loader
- `@babel/core` 是 babel 的核心库
- `@babel/preset-env` 预设了大部分新语法的插件，方便！如果不使用它，我们需要自己加很多 [plugins](https://babeljs.io/docs/en/plugins)，用箭头函数加一个，用解构赋值加一个...是不是好累。

安装完就在 webpack 里配置：

```js
// webpack.config.js

module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /node_modules/, // 排除依赖包，不然很慢
      use: {
        loader: "babel-loader",
        options: {
          presets: ["@babel/preset-env"]
        }
      }
    }
  ];
}
```

或者，把 `options` 单独写到 `.babelrc` 配置文件里（Babel 推荐用法），尤其当内容较多时。

```js
// webpack.config.js

module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /node_modules/,
      use: {
        loader: "babel-loader"
      }
    }
  ];
}
```

`.babelrc` 文件里就是 options 对象（注：后续均默认在单独的`.babelrc` 里修改）：

```js
{
  presets: ["@babel/preset-env"];
}
```

presets 里的 preset 也可以有 options。最好给 `@babel/preset-env` 的 options 配置个 `targets`，告诉 babel 你想要兼容哪些版本的哪些浏览器。比如，兼容全球使用量在 0.5% 以上尚未废弃的浏览器到最后 2 个版本。

```js
{
  presets: [
    [
      "@babel/preset-env",
      {
        targets: "> .5%, not dead, last 2 version"
      }
    ]
  ];
}
```

当然，用百分比是全球的使用情况，我们也可以根据我们国家的浏览器使用情况（可参考 [statcounter](http://gs.statcounter.com/browser-version-market-share/all/china/#monthly-201812-201902-bar)），自行加浏览器列表：

```js
{
  presets: [
    [
      "@babel/preset-env",
      {
        targets: {
          chrome: "58",
          ie: "11"
        }
      }
    ]
  ];
}
```

## 自行配置 plugins

当用了一些新句法，而 `@babel/preset-env` 不包含其所需的 plugin，那我们就要自己安装和配置。一般打包时会提示缺少哪个插件，babel 相关的就到 [Babel 官网](https://babeljs.io/) 搜索插件名查看使用方法。

比如用了还是实验性（[Experimental](https://babeljs.io/docs/en/plugins#experimental)）的 class decorator 装饰器。

```js
// use class decorator
@annotation
class MyClass {}

function annotation(target) {
  target.annotated = true;
}
```

就需要安装 [`@babel/plugin-proposal-decorators`](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)，然后给 `babel-loader` 的 options 配置 plugins 属性。

```shell
yarn add -D @babel/plugin-proposal-decorators
```

```js
{
  presets: ['@babel/preset-env'],
  plugins: ['@babel/plugin-proposal-decorators']
}
```

可以再给这个插件配置 options，`legacy` 属性设置为 `true`，表示使用 stage 1 的装饰器句法和行为。

```js
{
  presets: ['@babel/preset-env'],
  plugins: [
    ['@babel/plugin-proposal-decorators', { legacy: true }]
  ]
}
```

## @babel/polyfill

前面的基本配置，babel 只转换了 JavaScript 的新句法(syntax)，一些新的内置对象（比如 Promise、Symbol、Proxy、Set、Map 等）、静态方法（比如 `Array.from` `Object.assign`）、实例方法（比如 `Array.prototype.includes`）和构造器函数（generator functions）等并不会被转译。

可以引入 `@babel/polyfill` 来实现这部分的兼容，它会模拟一个 ES2015+ 的环境。polyfill 会在运行源代码之前运行，所以是作为 dependency，安装时不用加 `-D`。

```shell
yarn add @babel/polyfill
```

`@babel/polyfill` 提供了那么多新特性的 polyfill，打包后会变得很大。通常我们也并不会用到所有的新特性，那能不能最后打包进去的，只有我们用到的那些呢？

给 `@babel/preset-env` 的 options 配置 [`useBuiltIns`](https://babeljs.io/docs/en/babel-preset-env#usebuiltins)，告诉 `@babel/preset-env` 如果处理 polyfills。

若配置为 `useBuiltIns: 'usage'`，表示一个文件里用到了哪些新特性，就在该文件里导入哪些新特性的 polyfill。这样就不会把没用到的 polyfill 打包进去了。

```js
{
  presets: [
    [
      "@babel/preset-env",
      {
        useBuiltIns: "usage"
      }
    ]
  ];
}
```

## @babel/plugin-transform-runtime

Babel 会使用小的辅助函数（helpers）来执行常见的功能，比如 `_extend`。默认情况下，会添加到每个需要它们的文件中。

为了避免这种代码冗余，可以使用 @babel/runtime 作为单独的模块。为了避免 Babel 在每个文件注入辅助函数，需要 `@babel/plugin-transform-runtime`，让所有辅助函数都引用 `@babel/runtime` 模块，且 `@babel/runtime` 会被编译到打包后的文件中。

这个插件的另一个作用是，给代码提供一个沙盒环境。如果我们用了 `@babel/polyfill` 来提供一些内置对象（built-ins）比如 `Promise`、`Map`，这些将会污染全局环境。`core-js` 包含了这些内置对象，插件可以帮我们无缝地使用。

## @babel/runtime 和 @babel/runtime-corejs2

> `@babel/runtime` is a library that contain's Babel modular runtime helpers and a version of `regenerator-runtime`.

> `@babel/runtime-corejs2` is a library that contain's Babel modular runtime helpers and a version of `regenerator-runtime` as well as `core-js`.

其实 `@babel/runtime-corejs2` 看起来就像是 `@babel/runtime` 的升级版，多了一个功能。

两者都与 `@babel/plugin-transform-runtime` 搭配使用，实现运行时辅助函数的模块化，即复用 Babel 的辅助函数，避免代码臃肿；也都可以把 genetator/async 函数转换为不会污染全局环境的 regenerator runtime。

不同在于，对 Promise、Symbol 之类的处理：

- `@babel/runtime-corejs2` 会使用 `core-js` 的库函数（library functions）来实现，相比于 @babel/polyfill，不会造成全局命名空间污染（global namespace pollution）。
- 如果是 `@babel/runtime`，则不作处理，假定由用户提供相关 polyfill。

【注意】实例方法，比如 `Array.prototype.includes`（`"foobar".includes("foo")`），两种 runtime 都不能转换，需要 `@babel/polyfill` 来处理。（[Array.prototype.includes 兼容情况](https://caniuse.com/#search=Array.prototype.includes)）

【More】根据 Babel 的源码，其实 `@babel/polyfill` 和 `@babel/runtime-corejs2` 里的 `core-js`，用的都是第三方库[core-js](https://github.com/zloirock/core-js/tree/v2.5.7)。 [`@babel/polyfill`](https://github.com/babel/babel/blob/v7.3.0/packages/babel-polyfill/src/index.js) 用的是全局的 polyfills (`require('core-js')`)；`@babel/runtime-corejs2` 用的是模块化的 polyfills （`require('core-js/library')`）。后者的优点就是不会污染全局命名空间。

### 使用`@babel/runtime`

安装为生产环境的依赖，而不是开发依赖。

```js
yarn add @babel/runtime
yarn add -D @babel/plugin-transform-runtime
```

与 `@babel/plugin-transform-runtime` 配合使用，在 `.babelrc` 文件里添加插件：

```js
{
  presets: ["@babel/preset-env"],
  plugins: ["@babel/plugin-transform-runtime"]
}
```

### 使用 `@babel/runtime-corejs2`

安装为生产环境的依赖，而不是开发依赖。

```js
yarn add @babel/runtime-corejs2
yarn add -D @babel/plugin-transform-runtime
```

与 `@babel/plugin-transform-runtime` 配合使用，在 `.babelrc` 文件里添加插件，并一定要将 `corejs` 选项属性的值设置为 2，因为默认值是 `false`，不使用 `core-js` 的库函进行数转换。

```js
{
  presets: ["@babel/preset-env"],
  plugins: [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 2
      }
    ]
  ]
}
```

终极拷问👇：

Q：所以 `@babel/polyfill` 和 `@babel/plugin-transform-runtime` 用哪个？还是一起用？

A：按需使用🌝。

|                                                                | 内置对象 | 构造器函数 | 实例方法 | 污染全局环境           | 模块化 helpers |
| -------------------------------------------------------------- | -------- | ---------- | -------- | ---------------------- | -------------- |
| `@babel/polyfill`                                              | 支持     | 支持       | 支持     | 污染                   | 不支持         |
| `@babel/plugin-transform-runtime` 和 `@babel/runtime`          | 不支持   | 支持       | 不支持   | 不处理内置对象，不污染 | 支持           |
| `@babel/plugin-transform-runtime` 和 `@babel/runtime-corejs-2` | 支持     | 支持       | 不支持   | 沙盒环境，不污染       | 支持           |

## 参考文档

- [Webpack babel-loader](https://webpack.js.org/loaders/babel-loader/#root)
- [Babel docs](https://babeljs.io/docs/en/)

> 彩蛋🥚：webpack 5.0 的代码库正频繁更新中🌝，仿佛听见它快马加鞭🐎赶来的马蹄声，还在写 webpack 4 笔记的我👵…… （大喊学不动了！🙊误），当然不慌啊，基本的东西搞清楚，后面的更新就容易啦~ （放心，还有 Vue 3.0 呢）