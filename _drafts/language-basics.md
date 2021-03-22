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
    - [The Number Type](#the-number-type)
    - [The String Type](#the-string-type)
    - [The Symbol Type](#the-symbol-type)
    - [The Object Type](#the-object-type)
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

2. **Data types and their specific conversions**

    If the value is omitted or is `0`, `-0`, `null`, `false`, `NaN`, `undefined`, or the empty string (`""`), the object has an initial value of `false`. All other values, including any object, an empty array (`[]`), or the string `"false"`, create an object with an initial value of `true`.

    DATA TYPE | VALUES CONVERTED TO TRUE | VALUES CONVERTED TO FALSE
    :- | :- | :-
    Boolean | true | false
    Number | Any nonzero number(including infinity) | 0, NaN
    String | Any nonempty string | ""(empty string)
    Undefined | n/a | undefined
    Symbol | Any symbol | /
    Object | Any object | null

3. Flow-control statements, such as the if statement, **automatically** perform this Boolean conversion.

### The Number Type

1. ECMAScript uses the **IEEE–754** format to represent both integers and floating-point values (also called double–precision values in some languages).

2. **Octal Literal**: For an octal literal, the first digit must be a zero (0) If a number out of this range is detected in the literal, then the leading zero is ignored and the number is treated as a decimal. Octal literals are invalid when running in strict mode and will cause the JavaScript engine to throw a syntax error.

3. **Hexademical Literal**: For an hexadecimal literal, you must make the first two characters 0x (case insensitive).

4. Floating-Point: Because storing floating-point values uses twice as much memory as storing integer values, ECMAScript always looks for ways to **convert values into integers**. When there is no digit after the decimal point, the number becomes an integer. Another important thing is, adding 0.1 and 0.2 yields 0.30000000000000004 instead of 0.3. So `0.1 + 0.2 === 0.3` returns false. **Never compare float-point values**!

5. **E-Notion**: For very large or very small numbers, floating-point values can be represented using e-notation, such as `let floatNum = 3.125e-7`;

6. **Range of Values**: The largest number is stored in `Number.MIN_VALUE`, while the smallest is stored in `Number.MAX_VALUE`. Any calculation (such as `1 / 0`) result out this range will get the special value `infinity` which stored in `Number.POSITIVE_INFINITY` or `-infinity` which stored in `Number.NEGATIVE_INFINITY`. To determine if a value is finite, there is the `isFinite` function.

7. `NaN`: It is used to indicate when an **operation** intended to return a number has failed (**as opposed to throwing an error**). For example, dividing any number by 0 typically causes an error in other programming languages, halting code execution. In ECMAScript, `0 / 0` results in a `NaN`, which allows other processing to continue. It has several properties:
    1. Any operation involving `NaN` always returns `NaN`.
    2. Second, NaN is not equal to any value, including NaN. You should use `isNaN` to determine whether a varivable is `NaN`: `cosole.log(isNaN("blue")) // false`

8. **Number Conversions**: There are three functions to convert nonumeric values into numbers: the `Number()` casting function, the`parseInt()`function, and the `parseFloat()` function.  The first function, Number(), can be used on any data type; the other two functions are used specifically for converting strings to numbers.

9. `Number()`: The `Number()` function performs conversions based on these rules:
    1. When applied to **Boolean** values, true and false get converted into 1 and 0, respectively.
    2. When applied to **numbers**, the value is simply passed through and returned.
    3. When applied to `null`, Number() returns 0.
    4. When applied to `undefined`, Number() returns `NaN`.
    5. When applied to **symbols**, throw `TypeError`.
    6. When applied to **strings**, the following rule are applied:
        1. leading and end white space is ignored.
        2. If the string contains only numeric characters, optionally preceded by a plus or minus sign, it is always converted to a **decimal number**, so `Number("+123")` becomes 123, `Number("011")` becomes 11 (note: **leading zeros are ignored**)
        3. If the string contains a valid floating-point format, such as `"1.1"`, it is converted into the appropriate floating-point numeric value(**leading zeros are ignored**)
        4. If the string contains a valid hexadecimal format, such as `"0xf"`, it is converted into an integer that matches the hexadecimal value.
        5. If the string is empty (contains no characters), it is **converted to 0**.
        6. If the string contains anything other than these previous formats, it is converted into `NaN`.
    7. When applied to **objects**, the `valueOf()` method is called and the returned value is converted based on the previously described rules. If that conversion results in `NaN`, the `toString()` method is called and the rules for converting strings are applied.

    ```javascript
    let num1 = Number(" +123");  // 123
    let num2 = Number("011");  // 11
    let num3 = Number("0xff");  // 255
    let num4 = Number("");  // 0
    ```

10.  The *unary plus* operator, works the same as the `Number()` function

11.  `parseInt()`: If this first non-wihtespace character isn’t a number, the minus sign, or the plus sign, `parseInt()` always returns `NaN`. Otherwise, conversion goes on to the second character and continues on until either the end of the string is reached or a nonnumeric character is found.

    ```javascript
    let num1 = parseInt(" 1234blue");  // 1234
    let num2 = parseInt("");  // NaN
    let num3 = parseInt("0xA");  // 10 - hexadecimal
    let num4 = parseInt(22.5);  // 22
    ```

12. Radix of `parseInt`: All of the different numeric formats can be confusing to keep track of, so `parseInt()` provides a second argument: the radix (number of digits). In fact, by providing the hexadecimal radix, you can leave off the leading "0x". It’s advisable to **always include a radix** to avoid errors.

    ```javascript
    let num = parseInt("oxAF");  // 175
    let num = parseInt("oxAF", 16);  // 175
    let num = parseInt("AF", 16);  // 175
    let num = parseInt("AF");  // NaN 
    ```

13. `parseFloat()`: It works in a similar way to `parseInt()`, except:
    1. A decimal point is valid the first time it appears, but a second decimal point is invalid and the rest of the string is ignored
    2. No radix mode. Hexadecimal numbers always become 0.
    3. If the string represents a whole number (no decimal point or only a zero after the decimal point), `parseFloat()` returns an integer.(checkout the 4th note)
    
    ```javascript
    let num1 = parseFloat("123blue");  // 123 - integer
    let num1 = parseFloat("0xA");  // 0
    let num2 = parseFloat("22.34.5")  // 22.34 - float-point
    let num2 = parseFloat("0908.5")  // 908.5
    let num2 = parseFloat("0908.5")  // 908.5
    ```

### The String Type

1. String is a sequence of 16-bit **Unicode** characters.

2. **Escape Sequenc**e: There are several charater literals to represent useful characters. Except the common `\n`, `\t` ..., there are two uncommon literals:
    1. `\xnn`: A character represented by hexadecimal code `nn` (where `n` is a hexadecimal digit 0-F). Example: `"\x41"` is equivalent to `"A"`. `const length = "123\x41".length; // 4`
    2. `\unnnn`:  A Unicode character represented by the hexadecimal code nnnn (where `n` is a hexadecimal digit 0-F). Example: `\u03a3` is equivalent to the Greek character `Σ`.

3. **Converting to a String**: There are two fundamental ways to convert a value into a string: `toString` and `String()`.

4. `toString`: 
   1. The `toString()` method is available on any values except `undefined` and `null`. Call `toString` on a string simply returns a copy of itself.
   2. In most cases, `toString()` doesn’t have any arguments. However, when used on a number value, `toString()` actually accepts a single argument: the radix in which to output the number. For example: `console.log(10.toString(16));  // "a"`

5. `String()`: If you’re not sure that a value isn’t `null` or `undefined`, you can use the `String()` casting function, which always returns a string regardless of the value type. The `String()` function follows these rules:
   1. If the value has a `toString()` method, it is called (with no arguments) and the result is returned.
   2. If the value is `null`, `"null"` is returned.
   3. If the value is `undefined`, `"undefined"` is returned.

6. **Adding an empty string**: You can also convert a value to a string by adding an empty string (`""`) to that value using the plus operator.
    ```javascript
    let result = "" + 123; 
    console.log(typeof result)  // string
    ```
7. **Template Literals**: Unlike their single and double quoted counterparts, template literals **respect new line characters**, and **can be defined spanning multiple lines**:

    ```javascript
    let myMultiLineString = 'first line\nsecond line';
    let myMultiLineTemplateLiteral = `first line second line`;
    console.log(myMultiLineString); 
    // first line
    // second line"
    console.log(myMultiLineTemplateLiteral); 
    // first line
    // second line"
    ```

8. **Template Literals Notes**: Because template literals will exactly match the whitespace inside the backticks, special care will need to be applied when defining them.

    ```javascript
    // This template literal has 25 spaces following the line return character 
    let myTemplateLiteral = `first line 
                             second line`;
    console.log(myTemplateLiteral.length); // 47
    ```

9. *Template Literals Interpolation**: The value being interpolated will eventually be coerced into a string using `toString()`. Additionally, templates can safely **interpolate their previous value**:
    ```javascript
    let foo = { toString: () => 'World' }; 
    console.log(`Hello, ${ foo }!`);  // Hello, World!

    let value = ''; 
    function append() { value = `${value}abc` console.log(value); }
    append(); // abc 
    append(); // abcabc
    append(); // abcabcabc
    ```

10. **Template Literal Tag Functions**: It allows to define custom interpolation behavior by define *tag functions*. A tag function is defined as a **regular function** and is applied to a template literal by being prepended to it.  The tag function will be passed the template literal split into its pieces: the first argument is an array of the raw strings, and the remaining arguments are the results of the evaluated expressions.
    
    ```javascript
    let a = 6;
    let b = 9;

    function simpleTag(strings, ...expressions) { 
      console.log(strings.length - expressions.lenghth);
      console.log(strings); 
      for(const expression of expressions) {
          console.log(expression);
      }

      return 'foobar';
    }

    let untaggedResult = `${ a } + ${ b } = ${ a + b }`; 
    let taggedResult = simpleTag`${ a } + ${ b } = ${ a + b }`;
    // 1
    // ["", " + ", " = ", ""] 
    // 6 
    // 9 
    // 15

    console.log(untaggedResult);  // "6 + 9 = 15" 
    console.log(taggedResult);  // "foobar"
    ```

11. Raw String: 
    1.  `String.raw` tag function gives you access to the raw tempolate literal contents. Through this function, you get exactly what the template looks like.

        ```javascript
        // \u00A9 is the copyright symbol
        console.log(`\u00A9`);  // ©
        console.log(String.raw`\u00A9`);  // \u00A9
        ```
    2. Inside the tag function, the raw values are also available as a property on each element in the string piece collection.
        ```javascript
        function printRaw(strings) { 
          console.log('Actual characters:'); 
          for (const string of strings) { 
            console.log(string);
          }

          console.log('Escaped characters;'); 
          for (const rawString of strings.raw) { 
            console.log(rawString);
          }
        }

        printRaw`\u00A9${ 'and' }\n`; 
        // Actual characters: 
        // ©
        // (newline)
        // Escaped characters: 
        // \u00A9
        // \n
        ```


### The Symbol Type

1. Symbol instances are **unique** and **immutable**.

2. The purpose of a symbol is to be a guaranteed unique identifier for object properties that does not risk property collision.

3. The only function of the string passed in is to facilitate debug. It has no effect on the symbols's definition or identity. Symbols do not have a literal string syntax.

    ```javascript
    let genericSymbol = Symbol(); 
    let otherGenericSymbol = Symbol();

    let fooSymbol = Symbol('foo'); 
    let otherFooSymbol = Symbol('foo');

    console.log(genericSymbol);  // Symbol()
    console.log(fooSymbol);  // Symbol(foo)
    console.log(genericSymbol == otherGenericSymbol);  // false 
    console.log(fooSymbol == otherFooSymbol);  // false 
    ```

4. Importantly, the `Symbol` function **cannot be used with the *new* keyword**. The purpose of this is to avoid symbol object wrappers, as is possible with Boolean, String, and Number, which support con-
structor behavior and instantiate a primitive wrapper object. Should you want to utilize an object wrapper, you can make use of the Object() function:

    ```javascript
    let myBoolean = new Boolean(); 
    console.log(typeof myBoolean); // "object"

    let mySymbol = new Symbol(); // TypeError: Symbol is not a constructor

    let mySymbol2 = Symbol(); 
    let myWrappedSymbol = Object(mySymbol);
    console.log(typeof myWrappedSymbol); // "object"
    ```

5. **Using the Global Symbol Registry**: 

    1. It is possible to create and reuse symbols in a **string-keyed** global symbol registry. This behavior can be achieved using `Symbol.for()`:
        ```javascript
        let fooGlobalSymbol = Symbol.for('foo');  // creates new symbol
        let otherFooGlobalSymbol = Symbol.for('foo');  // reuses existing symbol
        console.log(fooGlobalSymbol === otherFooGlobalSymbol); // true
        ```

    2. Compared with `Symbol`: Symbols defined in the global registry are totally distinct from symbols created using `Symbol()`, even if they share a description:
        ```javascript
        let localSymbol = Symbol('foo'); 
        let globalSymbol = Symbol.for('foo');
        console.log(localSymbol === globalSymbol); // false
        ```

    3. Description: The global registry requires string keys, so anything you provide as an argument to `Symbol.for()` will be converted to a string. Additionally, the key used for the registry will also be used as the symbol description.
        ```javascript
        et emptyGlobalSymbol = Symbol.for(); 
        console.log(emptyGlobalSymbol); // Symbol(undefined)
        ```
    
    4. `Symbol.keyFor()`: It is possible to check against the global registry using `Symbol.keyFor()`, which accepts a symbol and will return the global string key for that global symbol, or undefined if the symbol is not a global symbol.
        ```javascript
        // Create global symbol 
        let s = Symbol.for('foo'); 
        console.log(Symbol.keyFor(s)); // foo

        // Create regular symbol 
        let s2 = Symbol('bar');
        console.log(Symbol.keyFor(s2)); // undefined

        Symbol.keyFor(123); // TypeError: 123 is not a symbol
        ```

6. **Using Symbols as Properties**:


7. Symbol值只能转换为字符串值或者布尔值。

### The Object Type

---

## Operators

---

## Statements

---

## Functions

---

References

- Professional JavaScript for Web Developers 4th Edition