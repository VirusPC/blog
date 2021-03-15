# Language Basics

Table of contents

- [Language Basics](#language-basics)
  - [Syntax Best Practices](#syntax-best-practices)
  - [Variables](#variables)
    - [Declaration Styles and Best Practices](#declaration-styles-and-best-practices)
  - [Data Types](#data-types)
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

### Declaration Styles and Best Practices

---

## Data Types

---

## Operators

---

## Statements

---

## Functions

---

References

- Professional JavaScript for Web Developers 4th Edition