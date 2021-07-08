---
title: "Chapter 11: Promises and Async Functions - Introduction to Asynchronous Programming" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/05/27
---

# Introduction to Asynchronous Programming

- [Introduction to Asynchronous Programming](#introduction-to-asynchronous-programming)
  - [Fundamental Concept](#fundamental-concept)
  - [Synchronous vs. Asynchronous JavaScript](#synchronous-vs-asynchronous-javascript)
  - [Legacy Asynchronous Programming Patterns](#legacy-asynchronous-programming-patterns)
    - [1. Returning Asynchronous Values - `success`](#1-returning-asynchronous-values---success)
    - [2. Handling Failure - `failure`](#2-handling-failure---failure)
    - [3. Nesting Asynchronous Callbacks](#3-nesting-asynchronous-callbacks)
  - [Event Loop](#event-loop)

---


Throughout this chapter, examples make extensive use of **asynchronous logging** `setTimeout(console.log, 0, ...params)` to demonstrate order of operation and other asynchronous behavior characteristics.

Note: A browser’s console output will often print information about objects that is not otherwise available to the JavaScript runtime (such as the state of a promise).

## Fundamental Concept

1. **顺序(sequntial)执行** 和 **并发(concurrent)执行** 关注的是程序的执行顺序. 顺序执行是指一个任务从执行开始到结束始终占用该进程. 上一个开始执行的任务完成后, 当前任务才能执行, 任务严格按顺序执行. 并发执行是指无论上一个开始执行的任务是否完成, 当前任务都可以开始执行. 并发执行要求这些任务的执行顺序不影响最终结果, 任务轮流执行, 多个任务可以在同一时间段内发生.

2. **串行(serial)** 和 **并行(paralell)** 关注的是执行任务的能力. 串行是指同一时刻只能执行一个任务. 并行是指同一时刻可以执行多个任务.

3. **同步(synchronous)** 和 **异步(asynchronous)** 关注的是消息通信机制. 对于**消息发送方**, 所谓同步，就是在发出一个调用时，在没有得到结果之前， 该调用就不返回。异步则是相反，调用在发出之后，这个调用就直接返回了，所以没有返回结果.

4. 同步也被称为 **阻塞(blocking)**, 异步也被称为 **非阻塞(nonblocking)**. 阻塞或非阻塞是针对单个进程而言的, 消息的发送方进程和接收方进程区别对待(blocking/nobloking  与 send/receive 组合, 四种, 见操作系统相关书籍), 本文中只针对发送方.

5. 并行一定可以并发. 异步(非阻塞)是实现并发的手段之一.

<!-- 异步是实现并发的手段之一. 同步 (非阻塞) 同样可以实现并发, 参考 Java Netty -->

## Synchronous vs. Asynchronous JavaScript

```js
let x = 3;
setTimeout(() => x = x + 4, 1000);
```

1. **The need of asynchronous behavior**: Asynchronous behavior is borne out of the need to optimize for higher computational throughput in the face of **high-latency** operations. 比如, 把IO操作做成异步的. 这样当主进程执行到IO语句时, 通知另一个进程执行IO操作, 而自己不等待IO结果就继续执行剩下的语句. 当另一个进程的IO执行完毕后再将结果返回给主进程, 主进程再根据结果执行后续异步相关代码(如果主进程需要结果的话). 这样就避免了主进程一直等待耗时的IO操作, 先去执行与异步行为无关的其他操作.

2. **Drawback**: You are generally unable to assert when the system state will change after the callback is scheduled.

3. 浏览器中一个页面只有一个线程来执行脚本, 是**串行**的. 浏览器和Node.js提供了一些**异步**操作的 API, 如`setTimeout`, `XMLHttpRequest`[#](https://www.zhihu.com/question/408642963/answer/1356774295).

4. JavaScript 是单线程的, 只能有一条线程来执行脚本, **串行** 执行. 但是浏览器是多线程的. 所谓的 **异步** 就是借助浏览器的其他线程实现的. 如 `setTimeout()` 使用了浏览器的定时器线程, ajax 请求使用了浏览器的 HTTP 请求线程. `setTimeout()` 将函数丢给浏览器, 使其脱离js运行的主线程, 到达给定时间后再将函数丢回一个队列中, 排队等待主线程执行.
   
5. A concurrency model specifies how threads in the the system collaborate to complete the tasks they are are given. Different concurrency models split the tasks in different ways, and the threads may communicate and collaborate in different ways[#](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html#reactive-event-driven-systems). JavaScript 的**并发**模型是基于 [event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop) 的.

6. Promise, async/await 并不能将普通代码变成异步的, 它只是用来更方便的组织多个异步行为. async/await 是 generator 的语法糖.

## Legacy Asynchronous Programming Patterns

1. In early versions of the language, an asynchronous operation only supported definition of a **callback** function to indicate that the asynchronous operation had completed. The full version of the using of an async function as follows:
    ```js
    asyncfunc(values, success, failure);
    ```

2. **Serializing asynchronous behavior** was a common problem, usually solved by a codebase full of nested callback functions—colloquially referred to as ''**callback hell**''. See the following part: *Nest Asynchronous Callbacks*.



3. 由于早期 JavaScript 中异步编程只支持简单回调函数的形式, 没有 Promise, async/await, 这就给用户在处理某些场景下的相关问题时带来很大不便. 这些场景包括:

      1. 处理异步操作的返回值
      2. 处理异步操作的错误
      3. 嵌套异步回调

      下面三部分会讲一下传统回调函数是如何处理上述三个场景的以及产生的问题. 前两个场景的问题是，必须在异步行为初始化时就定制好处理结果的回调函数。后一个场景的问题是回调地狱。再往后面的文章会讲Promise是如何解决这两个问题的以及其他特性。

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

This format is already undesirable, as the callbacks must be defined when the asynchronous operation is initialized (the last line). By *Promise*, we can set the operations latter with `then`.

### 3. Nesting Asynchronous Callbacks

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

## Event Loop

1. js异步任务分微任务(micro task)和宏任务(task). 这是为了将异步队列任务划分优先级，使得微任务可以插队。 

占坑, 待填

---

Reference:

- Professional JavaScript for Web Developers 4th Edition
- [setTimeout ,xhr,event 线程问题](https://blog.csdn.net/iteye_4865/article/details/81717864)
- [怎样理解阻塞非阻塞与同步异步的区别？(从操作系统层面讲, 很详细))](https://www.zhihu.com/question/19732473/answer/241673170)
- [并发与并行的区别是什么？](https://www.zhihu.com/question/33515481/answer/452128444)
- [concurrency-glossary](https://slikts.github.io/concurrency-glossary/)
- [Introduction to web APIs](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction)
- [Concurrency model and the event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
- [Concurrency Models](http://tutorials.jenkov.com/java-concurrency/concurrency-models.html#reactive-event-driven-systems)
- [Single-threaded Concurrency](http://tutorials.jenkov.com/java-concurrency/single-threaded-concurrency.html)
- [关于js异步任务有哪些](https://www.zhihu.com/question/408642963/answer/1356774295)
- [JS为什么要区分微任务和宏任务？](https://www.zhihu.com/question/316514618)
