# Language Basics

Table of contents

- [Language Basics](#language-basics)
  - [Syntax Best Practices](#syntax-best-practices)
  - [Variables](#variables)
    - [Declaration Styles and Best Practices](#declaration-styles-and-best-practices)
  - [Data Types](#data-types)
    - [The `typeof` Operator](#the-typeof-operator)
    - [The Undefined Type](#the-undefined-type)
    - [The Null Type](#the-null-type)
    - [The Boolean Type](#the-boolean-type)
    - [The Symbol Type](#the-symbol-type)
  - [Operators](#operators)
  - [Statements](#statements)
  - [Functions](#functions)

---

## Syntax Best Practices

1. You may also specify just a function to execute in strict mode by including the pragma at the top of the function body:

    ```js
    function doSomething() {
      "use strict"
      // function body
    }
    ```

2. Even though a semicolon is not required at the end of statements, you should always include one. Including semicolons:

    1. Helps prevent errors of omission. Such as not finishing what you were typing.
    2. Allows Developers to compress ECMSASCript code by removing extra white space.
    3. Including semicolons also improves performance in certain situations. Because parsers try to correct syntax errors by inserting semicolons where they appear to belong.

3. It is considered a best practice to always use code blocks with control statements, even if there’s only one statement to be executed.

---

## Variables

ECMAScript variables are loosely typed, meaning that a variable can hold any type of data. However, it’s not recommended to switch the data type that a variable contains.

There are three keywords that can be used to declare a variable: `var`, which is available in all ECMAScript versions, and `const` and `let`, which were introduced in ECMAScript 6.

Keyword\Feature| Declaration Scope | Declaration Hoisting | Redundant Declarations | Attach to `window` when declaring in the global context
:- | :-: | :-: | :-: | :-:
No Keyword | global | \ | yes | yes
`var` | function | yes | yes | yes 
`let` | block | no | no | no
`const` | block | no | no | no 

1. Strict mode throws a ReferenceError when an undeclared variable is assigned a value.

2. The declaration *redundancy* errors are not a function of order, and are not affected if `let` is mixed with `var`. The different keywords do not declare different types of variables—they just specify how the variables exist **inside the relevant scope**.

    ```js
    var name;
    let name; // SyntaxError

    let age;
    var age; // SyntaxError
    ```

3. You cannot rely on a conditional declaration pattern with this new ES6 declaration keyword.

4. For Loop: When using `var`, a frequent problem encountered was the singular declaration and modification of the iterator variable. This happens because the loop exits with its iterator variable still set to the value that caused the loop to exit: `5`. When the timeouts later execute, they reference this same variable, and consequently `console.log` its final value.

    ```js
    for (var i = 0; i < 5; ++i) {
      setTimeout(() => console.log(i), 0)
    }
    // You might expect this to console.log 0, 1, 2, 3, 4 
    // It will actually console.log 5, 5, 5, 5, 5
    ```

### Declaration Styles and Best Practices

1. Do not Use `var`. It will serve to enforce higher codebase quality thanks to careful management of variable scope, declaration locality, and const correctness.

2. Prefer `const` Over `let`. This allows for developers to more concretely reason about values that they know will never change, and also for quick detection of unexpected behavior in cases where the code execution attempts to perform an unanticipated value reassignment.


---

## Data Types

**six simple data types:** (also called primitive types): Undefined, Null, Boolean, Number, String, and Symbol(ES6).

**one complex data type:** Object, which is an **unordered list** of name–value pairs. 

### The `typeof` Operator

Using the `typeof` operator on a value returns one of the following strings:

- "*undefined*" if the value is undefined
- "*boolean*" if the value is a Boolean
- "*string*" if the value is a string
- "*number*" if the value is a number
- "*object*" if the value is an object (other than a function) **or null**
- **"*function*" if the value is a function**
- "*symbol*" if the value is a Symbol

Calling `typeof null` returns a value of "object", as the special value `null` is considered to be an **empty object reference**.

### The Undefined Type

1. The Undefined type has only one value, which is the special value `undefined`.

2. When compared with the literal value of `undefined`, the two are equal.

3. This type helps to formalize the difference between an empty object pointer (`null`) and an uninitialized variable.

4. Note that a variable containing the value of undefined is different from a variable that hasn’t been defined at all.

    ```js
    let message; // this variable is declared but has a value of undefined 
    //let age;

    console.log(message); // "undefined" 
    console.log(age); // causes an error
    ```

5. There are no operations can be performed on an undeclared variable except one: you can call `typeof` on it (calling `delete` on an undeclared
variable won’t cause an error, but this isn’t very useful and in fact throws an error in strict mode).

6. The value undefined is falsy.

    ```js
    let message;
    if(message) {
      // This block will not execute
    }
    ```

### The Null Type

1. The Null type has only one value: the special value `null`. 

2. When defining a variable that is meant to later hold an object, it is advisable to initialize the variable to `null`. That way, you can explicitly check for the value null to determine if the variable has been filled with an object reference at a later time.

3. The value undefined is a derivative of null, so ECMA-262 defines them to be superficially equal as follows (though keep in mind that this operator converts its operands for comparison purposes):
    ```js
    console.log(null == undefined); // true
    ```

4. The null type is falsy.

    ```js
    let message = null;
    if(message) {
      // This block will not execute
    }
    ```

### The Boolean Type

1. **All types of values have Boolean equivalents** in ECMAScript. To convert a value into its Boolean equivalent, the special Boolean() casting function is called, like this:

    ```js
    let message = "Hello world!";
    let messageAsBoolean = Boolean(message);
    ```

2. Data types and their specific conversions

    If the value is omitted or is `0`, `-0`, `null`, `false`, `NaN`, `undefined`, or the empty string (`""`), the object has an initial value of `false`. All other values, including any object, an empty array (`[]`), or the string `"false"`, create an object with an initial value of `true`.

    DATA TYPE | VALUES CONVERTED TO TRUE | VALUES CONVERTED TO FALSE
    :- | :- | :-
    Boolean | true | false
    Number | Any nonzero number(including infinity) | 0, NaN
    String | Any nonempty string | ""(empty string)
    Undefined | n/a | undefined
    Symbol | Any symbol | /
    Object | Any object | null


### The Symbol Type

1. Symbol值只能转换为字符串值或者布尔值。

---

## Operators

---

## Statements

---

## Functions

---

References

- Professional JavaScript for Web Developers 4th Edition