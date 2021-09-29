---
title: "Chapter 4 - Execution Context and Scope" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/09/11
---

# EXECUTION CONTEXT AND SCOPE

- [EXECUTION CONTEXT AND SCOPE](#execution-context-and-scope)
  - [Execution Context](#execution-context)
    - [Introduction](#introduction)
    - [Global Execution Context(GEC)](#global-execution-contextgec)
    - [Creation and Destruction of Execution Context](#creation-and-destruction-of-execution-context)
  - [Scope Chain Augmentation](#scope-chain-augmentation)
  - [Variable Declaration](#variable-declaration)
    - [Function Scope Declaration Using var](#function-scope-declaration-using-var)
    - [Block Scope Declaration Using let](#block-scope-declaration-using-let)
    - [Constant Declaration Using const](#constant-declaration-using-const)
    - [Identifier Lookup](#identifier-lookup)

---

## Execution Context

### Introduction
Each execution context has an associated variable object upon which all of its defined variables and functions exist. This object is not accessible by code but is used behind the scenes to handle data.

### Global Execution Context(GEC)

The global execution context is the outermost one. Depending on the host environment for an ECMAScript implementation, the object representing this context may differ. 

1. **In web browsers**, the global context is said to be that of the `window` object. 
    1. All global variables and functions defined with `var` are created as properties and methods on the `window` object.
    2. Declarations using `let` and `const` at the top level are not defined in the global context, but they are resolved identically on the scope chain. 
    3. In a tabbed browser, each tab is represented by its own *Window* Object.
        1. The global *Window* seen by JavaScript code running within a given tab always represents the tab in which the code is runnnig. 
        2. That said, even in a tabbed browser, some properties and methods still apply to the overall window that contains the tab, such as `resizeTo()` and innerHeight. Generally, anything that can't reasonably pertain to a tab pertains to the window instead.
  
2. **In NodeJS**, the global scope of a module is the module itself, which you can reference it with `this`. The `window` object in NodeJS is the `global` object.
    1. When you define a variable in the global scope of your nodeJS module, it will be **local** to this module.  
        ```js
        console.log(this);    // logs {}
        module.exports.foo = 5;
        console.log(this);   // log { foo:5 }
        ```
    2. We can access the global object in node using the `global` keyword. The `global` object exposes a variety of useful properties about the environment. Also this is the place where functions as `setImmediate` and `clearTimeout` are located.
        ```js
        console.log(global);
        ```
### Creation and Destruction of Execution Context

1. **Creation**: When a JavaScript engine executes a script, it creates execution contexts. Each execution context has two phases: the creation phase and the execution phase.
    1. When a script executes for the first time, the JavaScript engine creates a **global execution context**. 
    2. Create a this object binding which points to the global object above.
    3. Setup a memory heap for storing variables and function references.
    4. Store the function declarations in the memory heap and variables within the global execution context with the initial values as `undefined`.
   
2. **Destruction**: When an execution context has executed all of its code, it is destroyed, taking with it all of the variables and functions defined within it. That means the global context isn't destroy until the application exists, such as when a web page is closed or a web browser is shut down.

---

## Scope Chain Augmentation

---

## Variable Declaration

### Function Scope Declaration Using var

### Block Scope Declaration Using let

### Constant Declaration Using const

### Identifier Lookup

---

Reference:

- Professional JavaScript for Web Developers 4th Edition
- [What is the 'global' object in NodeJS](https://stackoverflow.com/questions/43627622/what-is-the-global-object-in-nodejs)
- [Introduction to the JavaScript execution context](https://www.javascripttutorial.net/javascript-execution-context/)
- [Window - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window)
- [JavaScript Execution Context](https://www.javascripttutorial.net/javascript-execution-context/)