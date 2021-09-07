---
title: "Chapter 3 - Syntax, Variables and Data Types" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/04/08
---

# Language Basics - Syntax, Variables and Data Types

Table of contents

- [Language Basics - Syntax, Variables and Data Types](#language-basics---syntax-variables-and-data-types)
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
    - [The BigInt type](#the-bigint-type)
    - [The Object Type](#the-object-type)

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
No Keyword | global | no | yes | yes
`var` | function | yes | yes | yes 
`let` | block | no | no | no
`const` | block | no | no | no 

1. 函数声明也会有声明提升(declaration hoisting). 但是 block 中的函数声明会产生奇怪的行为, 具体取决于浏览器实现. [#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block)

2. Strict mode throws a ReferenceError when an undeclared variable is assigned a value.

3. The declaration *redundancy* errors are not a function of order, and are not affected if `let` is mixed with `var`. The different keywords do not declare different types of variables—they just specify how the variables exist **inside the relevant scope**.

    ```js
    var name;
    let name; // SyntaxError

    let age;
    var age; // SyntaxError
    ```

4. You cannot rely on a conditional declaration pattern with this new ES6 declaration keyword.

5. For Loop: When using `var`, a frequent problem encountered was the singular declaration and modification of the iterator variable. This happens because the loop exits with its iterator variable still set to the value that caused the loop to exit: `5`. When the timeouts later execute, they reference this same variable, and consequently `console.log` its final value.

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

**seven simple data types:** (also called primitive types): Undefined, Null, Boolean, Number, String, Symbol(ES6) and BigInt(ES11).

**one complex data type:** Object, which is an **unordered list** of name–value pairs. 

### The `typeof` Operator

1. Using the `typeof` operator on a value returns one of the following strings:

    - "*undefined*" if the value is undefined
    - "*boolean*" if the value is a Boolean
    - "*string*" if the value is a string
    - "*number*" if the value is a number
    - "*object*" if the value is an object (other than a function) **or null**
    - **"*function*" if the value is a function**
    - "*symbol*" if the value is a Symbol

2. Before ECMAScript 2015, `typeof` was always guaranteed to return a string for any operand it was supplied with. Using `typeof` could never generate an error. However, with the addition of block-scoped `let` and `const`, using `typeof` on `let` and `const` variables (or using `typeof` on a `class`) in a block before they are declared will throw a *ReferenceError*. Block scoped variables are in a "temporal dead zone" from the start of the block until the initialization is processed, during which, it will throw an error if accessed. ES6之前，对一个未声明的变量名使用`typeof`，会认为这个变量是`undefined`，并返回`"undefined"`字符串。ES6之后，因为`let`和`const`的出现，对于未声明的变量，和在变量的 temporal dead zone 中对其使用`typeof`，会报引用错误。

    1. 直接对一个不会声明的变量使用`typeof`
        ```js
        console.log(typeof a); // "undefined"
        ```
    2. 对一个将要被`var`声明的变量使用`typeof`
        ```js
        console.log(typeof a); // Uncaught ReferenceError: Cannot access 'a' before initialization
        const a;
        ```
    3. 对一个将要被`const`或`let`声明的变量使用`typeof`
        ```js
        console.log(typeof a); // Uncaught ReferenceError: Cannot access 'a' before initialization
        var a;
        ```

3. Calling `typeof null` returns a value of "object", as the special value `null` is considered to be an **empty object reference**. However, `null instanceof Object` will return false.

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

5. javascript 中的 null，既是对象，又不是对象：
   
    ```js
    typeof null === 'object';  // true
    null instanceof Object;  // false
    null instanceof null;  // Uncaught TypeError: Right-hand side of 'instanceof' is not an object
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
    Boolean | `true` | `false`
    Number | Any nonzero number(including infinity) | `0`, `NaN`
    String | Any nonempty string | `""`(empty string)
    Undefined | n/a | `undefined`
    Symbol | Any symbol | /
    BigInt | any nonzero number | `0n`
    Object | Any object | `null`

3. Flow-control statements, such as the if statement, **automatically** perform this Boolean conversion.

### The Number Type

1. ECMAScript uses the **IEEE–754** format to represent both integers and floating-point values (also called double–precision values in some languages).

2. **Octal Literal**: For an octal literal, the first digit must be a zero (0) If a number out of this range is detected in the literal, then the leading zero is ignored and the number is treated as a decimal. Octal literals are invalid when running in strict mode and will cause the JavaScript engine to throw a syntax error.

3. **Hexademical Literal**: For an hexadecimal literal, you must make the first two characters 0x (case insensitive).

4. **Floating-Point**: Because storing floating-point values uses twice as much memory as storing integer values, ECMAScript always looks for ways to **convert values into integers** ([depends on the implementation of browsers](https://stackoverflow.com/questions/32276562/javascript-numbers-all-the-same-size-in-memory)). When there is no digit after the decimal point, the number becomes an integer. Another important thing is, adding 0.1 and 0.2 yields 0.30000000000000004 instead of 0.3. So `0.1 + 0.2 === 0.3` returns false. **Never compare float-point values**!

5. **E-Notion**: For very large or very small numbers, floating-point values can be represented using e-notation, such as `let floatNum = 3.125e-7`;

6. **Range of Values**: The largest number is stored in `Number.MIN_VALUE`, while the smallest is stored in `Number.MAX_VALUE`. Any calculation (such as `1 / 0`) result out this range will get the special value `infinity` which stored in `Number.POSITIVE_INFINITY` or `-infinity` which stored in `Number.NEGATIVE_INFINITY`. To determine if a value is finite, there is the `isFinite` function.

7. `NaN`: It is used to indicate when an **operation** intended to return a number has failed (**as opposed to throwing an error**). For example, dividing any number by 0 typically causes an error in other programming languages, halting code execution. In ECMAScript, `0 / 0` results in a `NaN`, which allows other processing to continue. It has several properties:
    1. Any operation involving `NaN` always returns `NaN`.
    2. Second, `NaN` is not equal to any value, including `NaN`. You should use `isNaN` to determine whether a varivable is `NaN`: `cosole.log(isNaN("blue")) // false`

8. **Number Conversions**: There are three functions to convert nonumeric values into numbers: the `Number()` casting function, the`parseInt()`function, and the `parseFloat()` function.  The first function, Number(), can be used on any data type; the other two functions are used specifically for converting strings to numbers.

9. `Number()`: The `Number()` function performs conversions based on these rules:
    1. When applied to **Boolean** values:  
       * `true` => `1`, `false` => `0`;
    2. When applied to **numbers**: 
       * the value is simply passed through and returned.
    3. When applied to **null**: 
       * => `0`.
    4. When applied to **undefined**: 
       * => `NaN`.
    5. When applied to **symbols**: 
       * throw `TypeError`.
    6. When applied to **strings**:
        1. leading and end white space is ignored.
        2. If the string contains only numeric characters, optionally preceded by a plus or minus sign, such as `"012"`, `"+12"`: 
           * => **decimal number**. 
        3. If the string contains a valid floating-point format, such as `"1.1"`: 
           * => **floating-point** numeric value(leading zeros are ignored)
        4. If the string contains a valid hexadecimal format, such as `"0xf"`: 
           * => **hexadecimal** value.
        5. If the string is empty (contains no characters): 
           * => `0`
        6. If the string contains anything other than these previous formats: 
           * => `NaN`.
    7. When applied to **objects**:
       1. `o.valueOf && ( (typeof o.valueOf()) !== object )` => `o.valueOf()`
       2. => `Number(o.toString())`

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

6. 默认的 `Object` 的 `toString()` 方法返回 `[object Object]`.
   
7. **Adding an empty string**: You can also convert a value to a string by adding an empty string (`""`) to that value using the plus operator. (See operator section in the next chapter)
    ```javascript
    let result = "" + 123; 
    console.log(typeof result)  // string
    ```
8. **Template Literals**: Unlike their single and double quoted counterparts, template literals **respect new line characters**, and **can be defined spanning multiple lines**:

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

9.  **Template Literals Notes**: Because template literals will exactly match the whitespace inside the backticks, special care will need to be applied when defining them.

    ```javascript
    // This template literal has 25 spaces following the line return character 
    let myTemplateLiteral = `first line 
                             second line`;
    console.log(myTemplateLiteral.length); // 47
    ```

10. *Template Literals Interpolation**: The value being interpolated will eventually be coerced into a string using `toString()`. Additionally, templates can safely **interpolate their previous value**:
    ```javascript
    let foo = { toString: () => 'World' }; 
    console.log(`Hello, ${ foo }!`);  // Hello, World!

    let value = ''; 
    function append() { value = `${value}abc` console.log(value); }
    append(); // abc 
    append(); // abcabc
    append(); // abcabcabc
    ```

11. **Template Literal Tag Functions**: It allows to define custom interpolation behavior by define *tag functions*. A tag function is defined as a **regular function** and is applied to a template literal by being prepended to it.  The tag function will be passed the template literal split into its pieces: the first argument is an array of the raw strings, and the remaining arguments are the results of the evaluated expressions.
    
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

12. Raw String: 
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
    1. Anywhere you can normally use a string or number property, you can also use a symbol. An object literal can only use a symbol as a property inside the computed property syntax.
        ```javascript
        let s1 = Symbol('foo');
        let s2 = Symbol('bar');
        let s3 = Symbol('baz');

        let o = {
          [s1]: 'fool val',
          property1: "hello",
        };
        o[s2] = 'bar val';

        Object.defineProperty(o, s3, { value: 'baz val' });
        console.log(Object.keys(o)); // ['property1']
        console.log(o);  
        //{
        //  property1: 'hello', 
        //  [Symbol(foo)]: 'fool val',
        //  [Symbol(bar)]: 'bar val'
        //}
        ```
    2. Just as `Object.getOwnPropertyNames()` returns an array of regular properties on an object instance, `Object.getOwnPropertySymbols()` returns an array of symbol properties on an object instance. The return values of these two methods are mutually **exclusive**. `Object .getOwnPropertyDescriptors()` will return an object containing both regular and symbol property descriptors. `Reflect.ownKeys()` will return both types of keys.
    ```javascript
    let s1 = Symbol('foo'), s2 = Symbol('bar');
    let o = {
      [s1]: 'foo val', 
      [s2]: 'bar val', 
      baz: 'baz val', 
      qux: 'qux val'
    };

    console.log(Object.getOwnPropertySymbols(o)); // [Symbol(foo), Symbol(bar)]
    console.log(Object.getOwnPropertyNames(o)); // ["baz", "qux"]
    console.log(Object.getOwnPropertyDescriptors(o)); // {baz: {...}, qux: {...}, Symbol(foo): {...}, Symbol(bar): {...}}
    console.log(Reflect.ownKeys(o));
    // ["baz", "qux", Symbol(foo), Symbol(bar)]
    ```
    3. Symbols are not lost if directly created and used as properties.Declining to keep an explicit reference to a property means that traversing all the object’s symbol properties will be required to recover the property key (The only way to get ):
    ```javascript
    let o = { 
      [Symbol('foo')]: 'foo val', 
      [Symbol('bar')]: 'bar val'
    };
    let barSymbol = Object.getOwnPropertySymbols(o) .find((symbol) => symbol.toString().match(/bar/));
    console.log(barSymbol);  // Symbol(bar)
    ```
8. **Well-Known Symbols**:  
    1. ECMAScript 6 also introduced a collection of well-known symbols that would be used throughout the language to **expose internal language behaviors** for direct access, overriding, or emulating. These well-known symbols exist as string properties on the Symbol factory function.
    2. One of the primary utilities of these well-known symbols is redefining them as to alter the behavior of the native language constructs.
    3. Each well-defined symbol property is non-writeable, non-enumerable, and non-configurabl.
    4. 为一个对象设置well-known symbols, 可以使得对象可以作为它们对应的操作的操作对象.
    5. 当目标对象作为某个操作的参数时, 会调用该对象上的, 操作所对应的well-known symbol的值. 注意下面代码中是调用了`Replace1`对象的`Symbol.replace`, 而非`"foo"`的. 如果代码改成调用`match`, 并把`Symbol.replace`改为`Symbol.match`, 会发现传入的`string2`为undefined, 因为`match`函数本就只能接受一个参数. 当然也可以直接设为`false`, 意为不支持该方法的调用.
        ```javascript
        class Replace1 {
            constructor(value) {
                this.value = value;
            }
            [Symbol.replace](string, string2) {
                console.log(string, string2); // foo 123
                return `s/${this.value}/${string2}/g`;
            }
        }
        console.log('foo'.replace(new Replace1('bar'), 123));
        // "s/bar/123/g"
        ```
    6. Well-known symbols 的值一般是一个函数(可能为非函数, 用于特殊用途, 见mdn `Symbol.match`), 其接受的参数依次为调用自己的对象, 以及对应函数除自己外的剩余的其他参数.
    7. Well-known symbols:
       1. `Symbol.iterator`[#1](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator)[#2](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols): It specifies the default iterator for an object. Used by `for...of`. Its value is a function or generator, with no arguments, and the returned iterator is used to obtain the values to be iterated.
       2. `Symbol.asyncIterator`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator): It specifies the default AsyncIterator for an object. If this property is set on an object, it is an async iterable and can be used in a `for await...of` loop. In many cases, this will take the form of an AsyncGenerator. There are currently no built-in JavaScript objects that have the `[Symbol.asyncIterator]` key set by default.s
       3. `Symbol.hasInstance`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/hasInstance): It is used to determine if a constructor object recognizes an object as its instance. The `instanceof` operator's behavior can be customized by this symbol.  Because the instanceof operator will seek the property definition on the prototype chain like any other property, it is possible to redefine the function on an inherited class as a *static* method. Its value is a function returns boolean.
       4. `Symbol.isConcatSpreadable`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/isConcatSpreadable):  It is used to configure if an object(array-like) should be flattened to its array elements when using the `Array.prototype.concat()` method. Its value is a boolean.
       5. `Symbol.match`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/match)It specifies the matching of a regular expression against a string. This function is called by the `String.prototype.match()` method.
       6. `Symbol.replace`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/replace): It specifies the method that replaces matched substrings of a string. This function is called by the `String.prototype.replace()` method. 
       7.  `Symbol.search`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/search): It specifies the method that returns the index within a string that matches the regular expression. This function is called by the `String.prototype.search()` method.
       8.  `Symbol.species`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/species): It specifies a function-valued property that the `constructor` function uses to create derived objects.
       9.  `Symbol.toPrimitive`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toPrimitive): It specifies a function valued property that is called to convert an object to a corresponding primitive value.
       10. `Symbol.toStringTag`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/toStringTag): It is a string valued property that is used in the creation of the default string description of an object. It is accessed internally by the `Object.prototype.toString()` method.
       11. `Symbol.unscopables`[#](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/unscopables): It is used to specify an object value of whose own and inherited property names are excluded from the with environment bindings of the associated object.

9.  Symbol值只能转换为字符串值或者布尔值. 不可转数字.

### The BigInt type

1. It created by appending `n` to the end of an integer literal, or by calling the `BigInt()` constructor (but without the new operator) and giving it an integer value or string value.
    ```js
    const previouslyMaxSafeInteger = 9007199254740991n

    const alsoHuge = BigInt(9007199254740991)
    // ↪ 9007199254740991n
    ```

2. A BigInt value is not strictly equal to a Number value, but it is loosely so:
    ```js
    0n === 0 // false
    0n == 0 // true

3. BigInt values are similar to Number values in some ways, but also differ in a few key matters: 
   1. Cannot be used with methods in the built-in Math object
   2. Cannot be mixed with a Number value in operations
   3. The precision of a BigInt value may be lost when it is coerced to a Number value.
   4. Bitwise operators are supported, except `>>>` (zero-fill right shift), as every BigInt value is signed.
   5. Unary operator(+) is not supported. [#](https://github.com/tc39/proposal-bigint/blob/master/ADVANCED.md#dont-break-asmjs)

4. A BigInt value behaves like a Number value in cases where:
   1. it is converted to a Boolean: via the Boolean function;
   2. when used with logical operators ||, &&, and !; or\
   3. within a conditional test like an if statement.

5. Usage recommendations:
   1. Only use a BigInt value when values greater than 253 are reasonably expected.
   2. Don’t coerce between BigInt values and Number values.

### The Object Type

1. If the constructor has no arguments, then the parentheses can be omitted safely (though that’s not recommended):: `let o = new Object`. 

2. Each Object instance has the following properties and methods:
    1. `constructor` 
    2. `hasOwnproperty(propertyName)`
    3. `isPrototypeof(object)`
    4. `propertyIsEnumerable(propertyName)`
    5. `toLocaleString()`
    6. `toString()`
    7. `valueOf()`
    8. ......

---

References

- Professional JavaScript for Web Developers 4th Edition
- [JavaScript numbers, all the same size in memory? - stackoverflow](https://stackoverflow.com/questions/32276562/javascript-numbers-all-the-same-size-in-memory)
- [BigInt - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
- [block - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/block)
- [有哪些明明是 bug，却被说成是 feature 的例子？ - justjavac的回答 - 知乎](https://www.zhihu.com/question/66941121/answer/247939890)
- [The history of “typeof null”](https://2ality.com/2013/10/typeof-null.html)
- [`typeof null` - MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof#typeof_null)
- [Errors with `typeof`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof#errors)