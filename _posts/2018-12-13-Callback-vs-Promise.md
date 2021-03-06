---
layout: post
title: 'Callback VS Promise'
subtitle: 'About who is more beautiful, callback or promise?'
author: 'Rushan'
header-img: 'img/post-bg-default-blue.jpg'
header-img-credit: 'Photo by Matteo Fusco on Unsplash'
header-img-credit-href: 'https://unsplash.com/photos/m94kn8Rp61Q'
header-mask: 0.4
tags:
  - JavaScript
  - Promise
---

需求：抓取 CNode 精华页第一个帖子的发帖人的个人信息。

思路：先从 `/api/v1/topics` 拿到精华页第一个帖子的数据，从数据里拿到作者的登录名称；再根据名称从 `/api/v1/user` 拿到用户信息。

实现：分别用传统 callback 写法和 Promise 写法实现，对比。

注：以下的 sample 代码均在 node.js 环境运行，记得先安装要使用的第三方库再试验代码。

## callback 写法

使用 [request](https://github.com/request/request) 作为 HTTP 客户端。

```js
// request(callback型)
const request = require('request')

function readAuthorInfo() {
  request(
    'https://cnodejs.org/api/v1/topics?tab=good&page=1&limit=1&mdrender=false',
    function(err, resonpse, body) {
      if (err) return
      let topicList = JSON.parse(body).data
      readUserInfo(topicList[0].author.loginname)
    }
  )
}

function readUserInfo(name) {
  request('https://cnodejs.org/api/v1/user/' + name, function(
    err,
    resonpse,
    body
  ) {
    if (err) return
    let info = JSON.parse(body).data
    console.log(info)
  })
}

readAuthorInfo()
```

## promise 写法

使用 [request](https://github.com/axios/axios) 作为 HTTP 客户端。

```js
// axios(promise型)
const axios = require('axios')

function readAuthorInfo() {
  axios
    .get(
      'https://cnodejs.org/api/v1/topics?tab=good&page=1&limit=1&mdrender=false'
    )
    .then(response => response.data)
    .then(topiclist => topiclist.data[0].author.loginname)
    .then(name => readUserInfo(name))
    .catch(err => console.log(err))
}

function readUserInfo(name) {
  axios.get('https://cnodejs.org/api/v1/user/' + name).then(response => {
    console.log(response.data.data)
  })
}

readAuthorInfo()
```

由于 axios 拿到的数据都是在 `response.data` 里，那就可以把这个提取成函数。

```js
// axios-2
const axios = require('axios')

function readData(response) {
  return response.data
}

function readAuthorInfo() {
  axios
    .get(
      'https://cnodejs.org/api/v1/topics?tab=good&page=1&limit=1&mdrender=false'
    )
    .then(readData)
    .then(topiclist => topiclist.data[0].author.loginname)
    .then(name => readUserInfo(name))
    .catch(err => console.log(err))
}

function readUserInfo(name) {
  axios
    .get('https://cnodejs.org/api/v1/user/' + name)
    .then(readData)
    .then(data => {
      console.log(data.data)
    })
}

readAuthorInfo()
```

还能再优化

```js
// axios-3
const axios = require('axios')

function readData(response) {
  return response.data
}

function readAuthorInfo() {
  axios
    .get(
      'https://cnodejs.org/api/v1/topics?tab=good&page=1&limit=1&mdrender=false'
    )
    .then(readData)
    .then(topiclist => topiclist.data[0].author.loginname)
    .then(readUserInfo)
    .catch(console.log)
}

function readUserInfo(name) {
  axios
    .get('https://cnodejs.org/api/v1/user/' + name)
    .then(readData)
    .then(data => console.log(data.data))
}

readAuthorInfo()
```

上面的 `.then(readUserInfo)` 和 `.catch(console.log)` 是怎么回事呢？

then 方法就是接收一个 callback 函数，并把参数传递给 callback 函数。

`.then(name => readUserInfo(name))` 这个 then 里面的函数，只是拿到参数，把参数直接给 readUserInfo，其他什么都没做。那就没必要多一层函数啦，直接把 readUserInfo 这个函数给 then 就好了，then 会把参数传给它的。

还能再优化吗？

axios 返回的都是 Promise 对象。试试写成链式调用。

```js
// axios-4
const axios = require('axios')

function readData(response) {
  return response.data
}

function readAuthorName() {
  return axios
    .get(
      'https://cnodejs.org/api/v1/topics?tab=good&page=1&limit=1&mdrender=false'
    )
    .then(readData)
    .then(topiclist => topiclist.data[0].author.loginname)
}

function readUserInfo(name) {
  return axios.get('https://cnodejs.org/api/v1/user/' + name).then(readData)
}

readAuthorName()
  .then(readUserInfo)
  .then(data => console.log(data.data))
  .catch(console.log)
```

链式调用是不是干净帅气？

如果 readAuthorName 和 readUserInfo 之间没有嵌套关系的话，是可以直接链式调用的。但如果它们之间有嵌套关系，2 个异步函数拿到结果的时间先后是不确定的，如果前一个异步函数还没返回结果，第二个异步函数就执行了，那就会 error。

嵌套问题，要交给 async/await 来解决。

```js
// axios-5
async function readAuthorInfo() {
  let name = await readAuthorName()
  let userInfo = await readUserInfo(name)
  return userInfo.data
}
readAuthorInfo()
  .then(console.log)
  .catch(console.log)
```

## 补充：如果返回值非 Promise，怎么办？

new 一个 Promise 对象，把异步函数塞到 Promise 对象的肚子里。

```js
// 想实现一个链式调用，但其中的 readFile 返回值不是 Promise
readFile()
  .asyncFun1()
  .then(asyncFun2)
  .then(console.log)
  .catch(console.log)
```

假设上面的 `readFile()` 是一个普通的异步函数，返回值不是 Promise，那我们可以这样改造：

```js
function readFile() {
  return new Promise(function(resolve, reject) {
    fs.readFile('path', function(err, content) {
      if (err) {
        return reject(err)
      }
      resolve(content)
    })
  })
}
```
