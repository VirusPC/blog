---
title: "Chapter 11: Promises and Async Functions - Introduction to Asynchronous Programming" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/05/27
---

# Introduction to Asynchronous Programming

- [Introduction to Asynchronous Programming](#introduction-to-asynchronous-programming)
  - [Synchronous vs. Asynchronous JavaScript](#synchronous-vs-asynchronous-javascript)
  - [Legacy Asynchronous Programming Patterns](#legacy-asynchronous-programming-patterns)
    - [1. Returning Asynchronous Values - `success`](#1-returning-asynchronous-values---success)
    - [2. Handling Failure - `failure`](#2-handling-failure---failure)
    - [3. Nesting Asynchronous Callbacks - nesting](#3-nesting-asynchronous-callbacks---nesting)

---

A browser’s console output will often print information about objects that is not otherwise available to the JavaScript runtime (such as the
state of a promise).

## Synchronous vs. Asynchronous JavaScript

```js
let x = 3;
setTimeout(() => x = x + 4, 1000);
```

1. We use the `setTimeout` function for example.
   
2. you are generally unable to assert when the system state will change after the callback is scheduled

## Legacy Asynchronous Programming Patterns

1. In early versions of the language, an asynchronous operation only supported definition of a **callback** function to indicate that the asynchronous operation had completed. 

2. Serializing asynchronous behavior was a common problem, usually solved by a codebase full of nested callback functions—colloquially referred to as ''**callback hell**''.

3. In the following code, after 1000ms, the JavaScript *runtime* will schedule the callback for execution by pushing it onto JavaScript’s *message queue*.
    ```js
    function double(value) {
      setTimeout(() => setTimeout(console.log, 0, value * 2), 1000);
    }
    double(3)  // 6 (printed after roughly 1000 ms)
    ```

4. **the full version of the using of an async function**:
    ```js
    asyncfunc(values, success, failure);
    ```

### 1. Returning Asynchronous Values - `success`

If the `setTimeout` returns a useful value, we should wrap the code depends on the value into a callback, and pass the callback to the asynchronous operation.
    
```js
function double(value, callback) { 
  setTimeout(() => callback(value * 2), 1000);
}
double(3, (x) => console.log(`I was given: ${x}`));
```

### 2. Handling Failure - `failure`

```js
function double(value, success, failure) { 
  setTimeout(() => { 
    try {
      if (typeof value !== 'number') {
        throw 'Must provide number as first argument';
      }
      success(2 * value);
    } catch (e) {
      failure(e);
    }
  }, 1000);
}

const successCallback = (x) => console.log(`Success: ${x}`);
const failureCallback = (e) => console.log(`Success: ${e}`);

double(3, successCallback, failureCallback);  // the callbacks must be defined before there.
```

This format is already undesirable, as the callbacks must be defined when the asynchronous operation is initialized (the last line).

### 3. Nesting Asynchronous Callbacks - nesting

This callback strategy does not scale well as code complexity grows. The “**callback hell**” colloquialism is well-deserved, as JavaScript codebases that were afflicted with
such a structure became nearly **unmaintainable**.

The callback situation is further complicated when access to the asynchronous values have dependencies on other asynchronous values. In the world of callbacks, this necessitates nesting the callbacks: 

```js
function double(value, success, failure) {
  setTimeout(() => {
    try {
      if (typeof value !== "number") {
        throw "Must provide number as first argument";
      }
      success(2 * value);  // nesting
    } catch (e) {
      failure(e);
    }
  }, 1000);
}

const successCallback = (x) => {
  double(x, (y) => console.log(`Success: ${y}`)); // nesting
};
const failureCallback = (e) => console.log(`Failure: ${e}`);

double(3, successCallback, failureCallback);

// Success: 12 (printed after roughly 2000ms)
```
---

Reference:

- Professional JavaScript for Web Developers 4th Edition
