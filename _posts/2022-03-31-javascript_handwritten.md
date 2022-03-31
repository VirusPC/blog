---
title: "JavaScript: 手写XXX"
categories: ['JavaScript']
tags: []
resource_path: /blog/assets/2022/03/31
---

{% include posts_hook.html %}

# JavaScript: 手写XXX

- [JavaScript: 手写XXX](#javascript-手写xxx)
  - [throttle](#throttle)
    - [不用`setTimeout`](#不用settimeout)
    - [用`setTimeout`](#用settimeout)
  - [debounce](#debounce)
  - [柯里化](#柯里化)
  - [`Promise.all`](#promiseall)
  - [`Promise.race`](#promiserace)
  - [`new` 操作符](#new-操作符)
  - [`call` 与 `apply`](#call-与-apply)
  - [`bind`](#bind)
  - [Promise try](#promise-try)
  - [Promise chain](#promise-chain)
  - [Promise chain + retry](#promise-chain--retry)
  - [async](#async)

---

## throttle

### 不用`setTimeout`
1. 每次调用，判断下curTIme-startTime与delay
2. 闭包
3. apply

```js
function throttle(func, delay){
  let startTime = 0;
  return function(){
    const curTime = new Date();
    if(curTime-startTime > delay){
      func.apply(this, arguments);
      startTime = curTime;
    }
  }
}
```

### 用`setTimeout`
1. timer，有则不执行, 执行完重置
2. 保存this和arguments

```js
function throttle(func, delay){
  let timer;
  return function(...args){
    if(timer) return;
    let self = this;
    // let args = arguments;
    setTimeout(() => {
      func.apply(self, args);
      clearTimeout(timer);
    }, delay);
  }
}
```

---

## debounce
1. timer，每次执行前重置
2. 保存this和arguments

```js
function debounce(func, delay){
  let timer;
  return function(...args){
    const self = this;
    // const args = arguments
    clearTimeout(timer);
    setTimeout(() => {
      func.apply(this, args)
    }, delay);
  }
}
```

---

## 柯里化

1. Function.length 返回该函数预接收参数的个数。
2. 闭包对参数做缓存
3. 返回函数中
   1. 合并arguments。 Array.prototype.slice.call(arguments) 浅拷贝
   2. 参数个数不够递归，够了执行

```js
// function curry(fn, ...args) {
//   length = fn.length;
//   return function(){
//     const newArgs = args.concat(Array.prototype.slice.call(arguments)); 
//     if(newArgs.length < length) {
//       return curry.call(this, fn, ...newArgs);
//     } else {
//       return fn.apply(this, newArgs);
//     }
//   }
// }

function curry(fn, ...args) {
  length = fn.length;
  return function(...newArgs){
    const mergedArgs = [...args, ...newArgs];
    if((mergedArgs.length) < length) {
      return curry.call(this, fn, ...mergedArgs);
    } else {
      return fn.apply(this, mergedArgs);
    }
  }
}
```

---

## `Promise.all`

1. 返回的是一个Promise
2. Promise的值通过调用resolve来设置
3. 注意判断长度

```js
function all(promises) {
    let len = promises.length;
    let res = [];
    let count = 0;
    if (len) {
        return new Promise(function (resolve, reject) {
            for(let i=0; i<len; i++) {
                let promise = promises[i];
                promise.then(response => {
                    res[i] = response;
                    ++count;
                    if(count === len) resolve(res);
                }, error => {
                   reject(error);
                })
            }
        })
    }
}

function all2(promises){
  const results = [];

  const merged = promises.reduce(
    (acc, p) => acc.then(() => p).then(r => results.push(r)),
    Promise.resolve(null));

  return merged.then(() => results);
};
```

---

## `Promise.race`

1. 只要一个`resolve`或`reject`了，后续的`resolve`或`reject`就无效了
   
```js
function race(...promises) {
  return new Promise((res, rej) => {
    promises.forEach(p => p.then(res).catch(rej));
  });
}
```

---

## `new` 操作符

1. 拷贝原型链
2. 2. 当构造函数返回非空对象(object或function)时, 即instanceof Object时，返回该对象。


```js
function New(func, ...args) {
    let res = Object.create(func.prototype, {});;
    let ret = func.apply(res, args);
    // if ((typeof ret === "object" || typeof ret === "function") && ret !== null) {
    if (ret instanceof Object){ 
      return ret;
    }
    return res;
}
```

---

## `call` 与 `apply`

1. 在context上增加一个属性，属性值为调用call/apply的函数 （新增属性不能和旧的冲突，可以用Symbol）
2. 执行context上新增的函数，保存执行结果
3. 删除该属性
4. 返回结果

```js
Function.prototype.call = function(context, ...args){
  const funcName = Symbol();
  context[funcName]  = this;
  const result = context[funcName](...args);
  delete context[funcName];
  return result; 
}

Function.prototype.apply = function(context, args){  // 与call的唯一不同之处
  const funcName = Symbol();
  context[funcName]  = this;
  const result = context[funcName](...args);
  delete context[funcName];
  return result; 
}
```

---

## `bind`

1. 返回的函数可能用于new，作为构造函数，此时this是resFn的实例
2. 通过中间构造函数tmp，浅拷贝原型，再赋予resFn。避免更改新函数的原型对象时，原函数的原型对象也会被改变。
   
```js
Function.prototype.bind = function(content, ...args) {
//    if(typeof this != "function") {
//        throw Error("not a function")
//    }
    let fn = this;
  
    let resFn = function() {
        return fn.apply(this instanceof resFn ? this : content,args.concat(...arguments) )
    }
    function tmp() {}
    tmp.prototype = this.prototype;
    resFn.prototype = new tmp();
    
    return resFn;
}
```

---

## Promise try

1. attempt 函数，执行promise，并且当失败时重新attempt

```js
function retry(p, times, delay) {
  return new Promise((resolve, reject) => {

    function attempt() {
      p().then(
        (value) => resolve(value),
        (reason) => times-- ? attempt() : resolve(reason)
      );
    }

    attempt();

  });
}
```

---

## Promise chain

```js
function chainPromise(promiseFactories) {
  const results = [];
  return new Promise((resolve, reject) => {
    const chainedPromise = promiseFactories.reduce(
      (pre, cur) => pre.then(() => cur())
        .then((value) => results.push(value), (reason) => reject(reason)),
      Promise.resolve(null)
    );
    chainedPromise.then(() => resolve(results));
  });
}
```

---

## Promise chain + retry

```js
function chainPromise(promiseFactories, times) {
  const results = [];
  return new Promise((resolve, reject) => {
    const chainedPromise = promiseFactories.reduce(
      (pre, cur) => pre.then(() => retry(cur, times))
        .then((value) => results.push(value), (reason) => reject(reason)),
      Promise.resolve(null)
    );
    chainedPromise.then(() => resolve(results));
  });
}
```

---

## async
```js
// https://www.ruanyifeng.com/blog/2015/05/async.html
function* asyncFun() {
  let a = yield Promise.resolve("A");
  let b = yield new Promise((resolve) => setTimeout(() => resolve("B"), 1000));
  return a + b;
}

function spawn(genF) {
  return new Promise(function (resolve, reject) {
    const gen = genF();
    // move one step forward
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch (e) {
        return reject(e);
      }
      if (next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(
        (v) => step(() => gen.next(v)),
        (e) => step(() => gen.throw(e))
      );
    }
    step(() => gen.next(undefined));
  });
}

spawn(asyncFun).then((value) => console.log(value));
```

---

参考资料:

1. [「中高级前端面试」JavaScript手写代码无敌秘籍](https://juejin.cn/post/6844903809206976520#heading-1)
2. [Implementing Promise.all](https://medium.com/@copperwall/implementing-promise-all-575a07db509a)
3. [async 函数的含义和用法 - 阮一峰](https://www.ruanyifeng.com/blog/2015/05/async.html)
4. [语雀笔记](https://www.yuque.com/pengcheng-fuigs/woidse)
