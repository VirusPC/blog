---
title: Redux
categories: ['前端']
tags: [‘react’, 'redux']
resource_path: /blog/assets/2020/
---

Redux
===

- [Redux](#redux)
  - [什么是 Redux](#什么是-redux)
  - [Redux 的工作流程](#redux-的工作流程)
  - [Store](#store)
  - [Redux DevTools](#redux-devtools)

---

什么是 Redux
---

组件很多时, 组件之间的数据传输很麻烦, 我们需要一个数据层框架. 目前最好的数据层框架是redux.

redux要求我们把所有的数据都放到公共的名为```Store```的存储区域. 当```Store```发生变化, 其他组件会感知知道. 有点像全局变量

![why neeed redux](./why_need_redux.png)

Redux = **Red**ucer + Fl**ux**

Redux 的工作流程
---

![redux flow](./redux_flow.png)

Store: 图书馆管理员
React Components: 借书者
Action Creators: 说的"要借什么书"这句话
Reducers: 图书放置位置的记录本

读数据: React Component 向 Store 发出读数据的 Action Creator, Store 查阅 Reducers, 告诉 Components 数据在哪.

改数据: React Component 向 Store 发出改数据的 Action Creator, Store 查阅 Reducer 以获取改数据的方式, 改完数据后通知 Components.

Store
---

创建 store, 并从 store 获取数据的基本流程.

1. reducer, ```src/store/reducer.js```:

  ```js
  const defaultState = {
      initialValue: 'initial value',
      list: ["c", "java", "javascript"],
  }

  export default (state = defaultState, action) => {
      return state;
  }
  ```

2. 利用 reducer 创建 store, ```src/store/index.js```:

  ```js
  import { createStore } from "redux"
  import reducer from './reducer'

  const store = createStore(reducer);

  export default store;
  ```

3. component 通过 store 获取 reducer 中的数据, ```src/component/Todolist.js```

  ```js
  import {Component} from 'react'
  import store from '../store'

  class TodoList extends Component {
    constructor(props){
      super(props);
      this.state = store.getState
    }
  }
  ```

Redux DevTools
---

1. 在chrome商店里找到这个插件, 安装
2. 修改代码中的```store```

  ```js
  const store = createStore(
      reducer,
      window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
  );
  ```
