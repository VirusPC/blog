---
title: "Chapter 4 - Primitive and Reference Values in Variables" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/05/16
---

# Working with primitive and reference values in variables

- [Working with primitive and reference values in variables](#working-with-primitive-and-reference-values-in-variables)
  - [Dynamic Properties](#dynamic-properties)
  - [Copying Values](#copying-values)
  - [Argument Passing](#argument-passing)
  - [Determining Type](#determining-type)

---

JavaScript is a **dynamically typed** language. Being loosely typed, a variable is literally just a name for a particular value at a particular time. Because there are no rules defining the type of data that a variable must hold, a variable’s value and data type can change during the lifetime of a script. 

ECMAScript variables may contain two different types of data: **primitive values** and **reference values**. Primitive values are simple atomic pieces of data, while reference values are objects that may be made up of multiple values. The first are said to be accessed by value, while the second

## Dynamic Properties

1. When you work with reference values, you can add , change, or delete properties and methods at any time.
  ```js
  let person = new Object();
  person.name = "Nicholas";  // add
  person.name = 'Tom';  // change
  delete person.name;  // delete
  ```

2. Primitive values can’t have properties added to them even though attempting to do so won’t cause an error. Here’s an example: 
  ```js
  let name = "Nicholas";
  name.age = 27;  // will throw error in strict mode
  console.log(name.age); // undefined
  ```

3. Note that the instantiation of **a primitive type can be accomplished using only the primitive literal form**. If you were to use the `new` keyword, JavaScript will create an Object type, but one that behaves like a primitive. 
  ```js
  let name1 = "Nicholas";
  let name2 = new String("Nicholas");
  let name3 = "Nicholas";
  name1.age = 27;
  name2.age = 26;
  console.log(name1.age); // undefined
  console.log(name2.age); // 26
  console.log(typeof name1); // string
  console.log(typeof name2); // object
  console.log(name1 === name2);  // false
  console.log(name1 === name3);  // true
  ```

## Copying Values

1. The copy of primitive values and reference values just behave like in the other languages.

## Argument Passing

1. All function arguments in ECMAScript are passed by value. If the value is primitive, then it acts just like a primitive variable copy, and if the value is a reference, it acts just like a reference variable copy.

2. When an argument is passed by value, the value is copied into a local variable (a named argument and, in ECMAScript, a slot in the `arguments` object).

## Determining Type

1. `typeof` is the best way to determine if a variable is a **primitive type**. For objects, there are two special cases:
    1. If the value is an function, then it returns "function". To be specific, ECMA-262 specifies that any object implementing the internal `[[Call]]` method should return "function" from `typeof`. Since regular expressions implement this method in these browsers, typeof returns "function".
    2. If the value is an object or `null`, then it returns "object".

2. For reference values, it is better to use `instanceof` operator to know what type of object it is.

---

Reference:

- Professional JavaScript for Web Developers 4th Edition
