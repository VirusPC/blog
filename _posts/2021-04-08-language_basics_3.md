---
title: "Chapter 3 - Statements and Functions" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/04/16
---

# STATEMENTS AND FUNCTIONS

- [STATEMENTS AND FUNCTIONS](#statements-and-functions)
  - [Statements](#statements)
    - [The if Statement](#the-if-statement)
    - [The do-while Statement](#the-do-while-statement)
    - [The while Statement](#the-while-statement)
    - [The for Statement](#the-for-statement)
    - [The for-in Statement](#the-for-in-statement)
    - [The for-of Statement](#the-for-of-statement)
    - [Labeled Statements](#labeled-statements)
    - [The break and continue Statements](#the-break-and-continue-statements)
    - [The with Statement](#the-with-statement)
    - [The switch Statement](#the-switch-statement)
  - [Functions](#functions)

---

## Statements 

### The if Statement

1. ECMAScript automatically converts the result of the condition expression into a Boolean by calling the `Boolean()` casting function on it.

2. It’s considered best coding practice to **always use block statements**, even if only one line of code is to be executed. Doing so can avoid confusion about what should be executed for each condition.

### The do-while Statement

1. Post-test loops such as this are most often used when the body of the loop should be executed **at least once** before exiting.

### The while Statement

### The for Statement

### The for-in Statement

1. The *for-in* statement is a strict iterative statement. It is used to enumerate the **non-symbol** keyed properties of an object.

2. It used to enumerate properties's name rather than value.

3. Object properties in ECMAScript are unordered, so the order in which property names are returned in a *for-in* statement cannot necessarily be predicted.

### The for-of Statement

1. The *for-of* statement is a strict iterative statement. It is used to loop through elements in an **iterable** object. 

2. The *for-of* loop will iterate in the order that the iterable produces values via its `next()` method.

3. Note that the *for-of* statement will throw an error if the entity that it is attempting to iterate over does **not support iteration**.

4. In ES2018, the *for-of* statement is extended as a *for-await-of* loop to support async iterables which produce promises.

### Labeled Statements

```js
start: for (let i = 0; i < count; i++) {
  console.log(i);
}
```

In this example, the label start can be referenced later by using the break or continue statement. Labeled statements are typically used with nested loops.

### The break and continue Statements

```js
let num = 0; 

outermost:
for (let i = 0; i < 10; i++) { 
  for (let j = 0; j < 10; j++) { 
    if (i == 5 && j == 5) { 
      continue outermost;
    } 
    num++; 
  }
}

console.log(num); // 95
```

Using labeled statements in conjunction with break and continue can be very powerful but can cause debugging problems if overused. Always use descriptive labels and try not to nest more than a few loops.

### The with Statement

1. The *with* statement sets the scope of the code within a particular object. The syntax is as follows: `with (expression) statement`.

2. The *with* statement was created as a convenience for times when a single object was being coded to over and over again.

    ```js
    let qs = location.search.substring(1); 
    let hostName = location.hostname;
    let url = location.href;
    ```

    It equals:

    ```js
    with(location) { 
      let qs = search.substring(1); 
      let hostName = hostname; 
      let url = href;
    }
    ```
  
  3. In strict mode, the *with* statement is **not allowed** and is considered a syntax error.
  
  4. It is widely considered a poor practice to use the *with* statement in production code because of its negative performance impact and the difficulty in debugging code contained in the with statement.

### The switch Statement

```js
switch ("hello world") { 
  case "hello" + " world":
    console.log("Greeting was found."); 
    break;
  case "goodbye":
    console.log("Closing was found."); 
    break; 
  default:
    console.log("Unexpected message was found.");
}
```

1. It has some unique characteristics in ECMAScript compared with other languages.
    1. The *switch* statement works with all data types
    2. The case values need not be constants; they can be variables and even expressions. 

2. The switch statement compares values using the **identically equal (==)** operator, so no type coercion occurs.

---

## Functions

1. Best practices dictate that a function either always return a value or never return a value. Writing a function that sometimes returns a value causes
confusion, especially during debugging.

2. Strict mode places several restrictions on functions. If any of these occur, it’s considered a syntax error and the code will not execute.
   1. No function can be named `eval` or `arguments`.
   2. No named parameter can be named `eval` or `arguments`.
   3. No two named parameters can have the same name.

3. Function 有声明提升(declaration hoisting).

4. 块内函数声明:
    1. ES5规定, 严格模式和非严格模式都不允许在块级作用域中进行函数声明, 只允许在函数作用域中声明. 但各浏览器在实际实现中都允许, 用function声明函数就像用var声明变量一样,会无视大括号, 即使块永远不会执行(如函数被`if(false){}`包裹着).
    2. ES6中, 严格模式下, 不允许在块级作用域中进行函数声明. 只有非严格模式下才允许.
    3. ES6中, 非严格模式下, 块内的函数声明的行为取决于浏览器具体实现.
        > In non-strict code, function declarations inside blocks behave strangely. Do not use them.

---

Reference:

- Professional JavaScript for Web Developers 4th Edition
- [block - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block)
