---
title: "Chapter 11: Promises and Async Functions - Async Functions" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/05/28
---

{% include posts_hook.html %}

# Async Functions 

- [Async Functions](#async-functions)
  - [Origin](#origin)
  - [Async Function Basics](#async-function-basics)
    - [The async keyword](#the-async-keyword)
    - [The await keyword](#the-await-keyword)
    - [Restrictions on await](#restrictions-on-await)
  - [Halting and Resuming Execution](#halting-and-resuming-execution)
  - [Strategies for Async Funcitons](#strategies-for-async-funcitons)
    - [Implmenting Sleep()](#implmenting-sleep)
    - [Maxmizing Parallelization](#maxmizing-parallelization)
    - [Serial Promise Execution](#serial-promise-execution)
    - [Stack Traces and Memory Management](#stack-traces-and-memory-management)
  - [Summary](#summary)

---

## Origin

Async functions, also referred to by the operative keyword pair “async/await,” are the application of the ES6 Promise paradigm to ECMAScript functions. **It allows for JavaScript code which is written in a synchronous fashion, but actually is able to behave asynchronously.**

What problem exists now? With promise, we write this kind of code:

```js
function handler(x) { console.log(x); } 
let p = new Promise((resolve, reject) => setTimeout(resolve, 1000, 3));
p.then(handler); // 3
```

Any subsequent code that wishes to access the value produced by the promise needs to be fed that value through a handler, which means being shoved into a handler function.

ES7’s async/await is intended to directly address the issue of organizing code which makes use of asynchronous constructs.

## Async Function Basics

### The async keyword

1. How to use? An async function can be declared by prepending the `async` keyword. This keyword can be used on function declarations, function expressions, arrow functions, and methods.

2. Using the async keyword will create a function that exhibits some asynchronous characteristics but overall is **still synchronously evaluated**. In all other respects such as argumetns and closures, it still exhibits all the normal behavior of a JavaScript function.

3. In an async function, whatever value is returned with the return keyword (or `undefined` if there is no return) will be effectively converted into a promise object with `Promise.resolve()`. An async function will always return a promise object. A thenable object will be “unwrapped” via the first argument provided to the `then()` callback. A non-thenable object will be passed through as if it were an already resolved promise. 

### The await keyword

### Restrictions on await

---

## Halting and Resuming Execution

---

## Strategies for Async Funcitons

### Implmenting Sleep()

### Maxmizing Parallelization

### Serial Promise Execution

### Stack Traces and Memory Management

---

## Summary


---

Reference:

- Professional JavaScript for Web Developers 4th Edition
