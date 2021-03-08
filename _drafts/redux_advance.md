---
title: Redux 进阶
categories: ['前端']
tags: [‘react’, 'redux']
resource_path: /blog/assets/2020/
---

# Redux 进阶

## Table of Contents

- [Redux 进阶](#redux-进阶)
  - [Table of Contents](#table-of-contents)
  - [UI 组件和容器组件](#ui-组件和容器组件)
  - [无状态组件](#无状态组件)
  - [Redux 中发送异步请求获取数据](#redux-中发送异步请求获取数据)
  - [使用 Redux-thunk 中间件实现 ajax 数据请求](#使用-redux-thunk-中间件实现-ajax-数据请求)
  - [什么是 Redux 的中间件](#什么是-redux-的中间件)
  - [Redux-saga 中间件使用入门](#redux-saga-中间件使用入门)
  - [如何使用 Rect- redux](#如何使用-rect--redux)
  - [使用 React-redux 完成 TodoList 功能](#使用-react-redux-完成-todolist-功能)

## UI 组件和容器组件

有时候, 我们把一个组件的渲染(```render```)和逻辑(其他方法)都放到一个组件中时, 组件会很难维护, 此时我们需要对组件进行拆分, 将其拆分成UI组件(傻瓜组件)和容器组件(聪明组件). *UI组件*只负责页面的渲染, *容器组件*只负责业务逻辑和数据的处理. 数据和监听函数在容器组件中定义后通过props传递给UI组件.

UI组件:

![UI comopnent](./ui-component.png)

容器组件:

![container comopnent](./container-component.png)

## 无状态组件

当一个组件里的方法只有```render```时, 我们可以将其写成一个函数, 称之为无状态组件. 上面定义的UI组件可以写成一个无状态组件, 它可以写成下面这样的函数形式:

![UI comopnent](./ui-component2.png)

无状态组件的优势在于它的性能比较高, 因为它就是一个简单函数, 而我们之前定义的组件是一个类.

## Redux 中发送异步请求获取数据

```js
componentDidMount = () => {
  store.subscribe(() => this.setState(store.getState()));
  axios.get("http://localhost:3000/data")
    .then((res) => {
      const data = res.data;
      const action = getInitListAction(data);
      store.dispatch(action);
    })
}
```

## 使用 Redux-thunk 中间件实现 ajax 数据请求

如果把一些异步的请求或者非常复杂的逻辑, 都放到组件中实现, 这个组件有时会显得过于臃肿, 我们希望这些代码移到统一的地方进行管理. [redux-thunk](https://github.com/reduxjs/redux-thunk) 这个中间件可以让我们把这些异步请求或复杂逻辑放到action中进行处理.

步骤一: 启用 Redux Thunk (同时也启动 Redux DevTools) :

```js
/* store.js */
import { createStore, applyMiddleware, compose } from "redux"
import reducer from './reducer'
import thunk from "redux-thunk"

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const store = createStore(
  reducer,
  composeEnhancers(
    applyMiddleware(
      thunk
    )
  )
);

export default store;;
```

步骤二: 正常情况下, actionCreator 应该返回一个对象. 用了 thunk 之后, 它可以返回一个函数了. 我们可以把复杂逻辑放到这个函数里. 这个函数有一个参数```dispatch```, 相当于```store.dispatch```. 我们在这个函数里写逻辑继续dispatch其他action来修改state即可.

```js
/* actionCreators.js */
export const getTodoList = () => {
  return (dispatch) => {
    axios.get("http://localhost:3000/data")
      .then((res) => {
        const data = res.data;
        const action = getInitListAction(data);
        dispatch(action);
      })
  }
}
```

步骤三: 之后在 component 中像普通 action 那样使用即可.

```js
/* TodoList.jsx */
componentDidMount = () => {
  store.subscribe(() => this.setState(store.getState()));
  const action = getTodoList();
  store.dispatch(action);
}
```

除了可以简化组件外, 这种方式还十分利于自动化测试.

## 什么是 Redux 的中间件

![Redux Data Flow](./redux-data-flow.png)

Redux 中间件是位于 Action 和 Store 之间. 不使用中间件时, action 是一个对象, 直接派发给 store. 有了中间件后, action可以是函数了, 这个函数实际上就是对```store.dispatch```的封装. dispatch 可以区分对象和函数. dispatch 一个函数, 不会直接将它传给 store, 它会直接让函数执行.

除了 redux-thunker 外, 还有 redux-logger (可以记录 action 每次发送的日志), redux-saga (类似 redux-thunker)等.

## Redux-saga 中间件使用入门

redux-saga 类似 redux-thunker, 但 redux-thunker 是把异步操作放入action中, redux-saga 的设计思路是单独将异步操作拆分出来放到专门的文件中进行管理

## 如何使用 Rect- redux

## 使用 React-redux 完成 TodoList 功能
