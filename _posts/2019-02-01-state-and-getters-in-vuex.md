---
layout: post
title: 'Vuex: State and Getters'
subtitle: 'About state and getters in vuex'
author: 'Rushan'
header-img: 'img/post-bg-default.jpg'
header-img-credit: 'Photo by Andy Holmes on Unsplash'
header-img-credit-href: 'https://unsplash.com/photos/LUpDjlJv4_c'
header-mask: 0.4
tags:
  - Vuex
---

## 访问 state

### 从 template 直接访问

```vue
<template>
  <h1>Create Event, {{ $store.state.user.name }}</h1>
</template>
```

### 通过 computed 访问

```js
computed: {
    userName() {
      return this.$store.state.user.name
    },
    userID() {
      return this.$store.state.user.id
    }
}
```

#### computed 里使用 mapState 辅助函数

要先从 vuex 引入 `mapState`。

```vue
<script>
import { mapState } from 'vuex'
</script>
```

具名，如果是 `state` 的属性，而不是属性的属性，可以用字符串代替函数。

```js
// 具名
computed: mapState({
  // 使用 mapState
  // userName: state => state.user.name,
  // userID: state => state.user.id,
  user: 'user',
  // categories: state => state.categories
  categories: 'categories' // 上一句的简写，如果引入的是state的直接属性，而不是属性的属性，可以用字符串代替函数。
})
```

也可以不具名，改对象为数组，用字符串作为元素。

```js
// 不具名
computed: mapState(['user', 'categories'])
```

如果需要设置其他计算属性，则对 `mapState` 使用 Object Spread Operator ([MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax))：

```js
computed: {
    localComputed() {
        return 'something'
    },
    ...mapState(['user', 'categories'])
}
```

## Getters

当我们访问 `state` 是想获取 它的派生值时，访问相应的 `getters`。

先在 store.js 里设置 `getters`:

```js
// store.js
state: {
    categories: [
      'sustainability',
      'nature',
      'animal welfare',
      'housing',
      'education',
      'food',
      'community'
    ]
},
getters: {
  catLength: state => {
    return state.categories.length
  }
}
```

在 computed 里使用 getters:

```js
computed: {
    catLength() {
        return this.$store.getters.catLength
    }
}
```

### 在一个 getter 中访问另一个 getter

getter 方法传入的的参数中，加入 `getters`，就能通过 `getters` 访问另一个 getter。

```js
// store.js
state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false },
      { id: 3, text: '...', done: true },
      { id: 4, text: '...', done: false }
    ]
},
getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    },
    activeTodosCount: (state, getters) => {
      return state.todos.length - getters.doneTodos.length
    }
}
```

### 动态 getter

原本静态的 getter 是返回一个普通的值，当我们想要给 getter 传参，那就让 getter 返回一个函数：

```js
// store.js
state: {
    events: [
      { id: 1, text: '...', organizer: '...' },
      { id: 2, text: '...', organizer: '...' },
      { id: 3, text: '...', organizer: '...' },
      { id: 4, text: '...', organizer: '...' }
    ]
},
getters: {
    getEventByID: state => id => {
      return state.events.find(event => event.id === id) // dynamic getters
    }
}
```

### 在 computed 使用 mapGetters 辅助函数

同样，要先从 vuex 引入 mapGetters

```vue
<script>
import { mapGetters } from 'vuex'
</script>
```

可以直接使用字符串：

```js
computed: mapGetters(['categoriesLength', 'getEventById'])
```

也可以对 getter 方法重命名：

```js
computed: mapGetters({
  catCount: 'categoriesLength',
  getEvent: 'getEventById'
})
```

同样，如果需要设置其他计算属性，则对 `mapGetters` 使用 Object Spread Operator ([MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax))：

```js
  computed: {
    localComputed () {
        return 'something'
    },
    ...mapGetters(['getEventByID'])
  }
```

## 总结

- 获取 `state` 的原始值
  - 在 template 直接通过 `$store.state` 访问
  - 通过 `this.$store.state` 访问
  - 在 computed 里借助 `mapState` 访问
- 获取 `state` 的派生值，通过 `getters`
  - 通过 `this.$store.getters` 访问
  - 在 computed 里借助 `mapGetters` 访问
