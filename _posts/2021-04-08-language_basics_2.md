---
title: "Chapter 3 - Operators" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/04/08
---

# Operators

When used on objects, operators typically call the `valueOf()` and/or `toString()` method to retrieve a value they can work with.

- [Operators](#operators)
  - [Unary Operators](#unary-operators)
    - [Increment/Decrement](#incrementdecrement)
    - [Unary Plus and Minus](#unary-plus-and-minus)
  - [Bitwise Operators](#bitwise-operators)
    - [Bitwise NOT](#bitwise-not)
    - [Bitwise AND/OR/XOR](#bitwise-andorxor)
    - [Left Shift](#left-shift)
    - [Signed Right Shift / Unsigned Right Shift](#signed-right-shift--unsigned-right-shift)
  - [Boolean Operators](#boolean-operators)
    - [Logical NOT](#logical-not)
    - [Logical AND](#logical-and)
    - [Logical OR](#logical-or)
  - [Multiplicative Operators](#multiplicative-operators)
    - [Multiply](#multiply)
    - [Divide](#divide)
    - [Modulus](#modulus)
  - [Exponentiation Operator](#exponentiation-operator)
  - [Additive Operators](#additive-operators)
    - [Add](#add)
    - [Subtract](#subtract)
  - [Relational Operators](#relational-operators)
  - [Equality Operators](#equality-operators)
    - [Equal and Not Equal](#equal-and-not-equal)
    - [Identically Equal and Not Identically Equal](#identically-equal-and-not-identically-equal)
  - [Conditional Operator](#conditional-operator)
  - [Assignment Operators](#assignment-operators)
  - [Comma Operator](#comma-operator)

---

## Unary Operators

Operators that work on only one value are called unary operators. They are the simplest operators in ECMAScript.

### Increment/Decrement

1. Prefix increment / decrement: the variable’s value is changed before the statement is evaluated. 

2. Postfix increment / decrement: the increment or decrement doesn’t occur until after the containing statement has been evaluated.

```js
let num1 = 2; 
let num2 = 20;
let num3 = num1-- + num2; 
let num4 = num1 + num2; 
console.log(num3); // 22
console.log(num4); // 21
```

3. If it used on non-numbers, converted it into a number using `Number()` first. If it used on `NaN`, it will return `NaN`.

### Unary Plus and Minus

1. `+`: When the unary plus is applied to a nonnumeric value, it performs the same conversion as the `Number()` casting function.

2. `-`: The unary minus operator’s primary use is to negate a numeric value, such as converting 1 into –1.

---

## Bitwise Operators

1. All numbers in ECMAScript are stored in IEEE–754 64-bit format, but the bitwise operations do not work directly on the 64-bit representation. Instead, the value is converted into a 32-bit integer, the operation takes place, and the result is converted back into 64 bits. To the developer, it appears that only the **32-bit integer** exists because the 64-bit storage format is transparent.

2. A curious side effect of this conversion is that the special values `NaN` and `Infinity` both are treated as equivalent to `0` when used in bitwise operations.

3. Negative numbers are also stored in binary code but in a format called two’s complement. (负数以二进制补码形式表示)

4. If a bitwise operator is applied to a nonnumeric value, the value is first converted into a number using the `Number()` function (this is done automatically) and then the bitwise operation is applied. The resulting value is a number.

### Bitwise NOT

```js
let num1 = 25; // binary 00000000000000000000000000011001
let num2 = ~num1; // binary 11111111111111111111111111100110
let num3 = -num1 - 1;
console.log(num2); // -26
console.log(nume); // -26
```

1. The bitwise NOT is represented by a tilde (`~`) and simply returns the one’s **complement** (反码) of the number. It negates the number and subtracts 1.

2. Realistically, though this returns the same result, the bitwise operation is much faster because it works at the very lowest level of numeric representation.

### Bitwise AND/OR/XOR

```js
let a = 25;           // 0000 0000 0000 0000 0000 0000 0001 1001
let b = 3;            // 0000 0000 0000 0000 0000 0000 0000 0011
let result1 = 25 & 3; // 0000 0000 0000 0000 0000 0000 0000 0001
let result2 = 25 | 3; // 0000 0000 0000 0000 0000 0000 0001 1011
let result3 = 25 ^ 3; // 0000 0000 0000 0000 0000 0000 0001 1010
console.log(result1); // 1
console.log(result2); // 27
console.log(result3); // 26
```

### Left Shift

```js
let oldValue = -2;            // 1111 1111 1111 1111 1111 1111 1111 1110 补码
let newValue = oldValue << 5; // 1111 1111 1111 1111 1111 1111 1100 0000 补码左移末尾补0, 不论正负
console.log(newValue);        // 1000 0000 0000 0000 0000 0000 0100 0000 原码 -64
```

1. 注意负数是对其补码进行移位.

2. 一个**有效的**左移最高位和数据最高位必然一致, 再加上其末尾永远补0, 故算术左移和逻辑左移一样。[#](https://zhuanlan.zhihu.com/p/97749860)

3. So, the left shift preserves the sign of the number it’s operating on.


### Signed Right Shift / Unsigned Right Shift

```js
let oldValue1 = -64;            // 1111 1111 1111 1111 1111 1111 1100 0000 补码, 有符号整数
let newValue1 = oldValue >> 5;  // 1111 1111 1111 1111 1111 1111 1111 1110 -2
let oldValue2 = -64;            // 1000 0000 0000 0000 0000 0000 0100 0000 补码, 无符号整数
let newValue2 = oldValue >>> 5; // 0000 0100 0000 0000 0000 0000 0000 0010, 补码, 13421772
```

---

## Boolean Operators

### Logical NOT

1. The logical NOT operator first converts the operand to a Boolean value using `Boolean()` and then negates it.

2. By using two NOT operators in a row, you can effectively simulate the behavior of the `Boolean()` casting function.

```js
console.log(!!"blue"); // true
console.log(!!NaN); // false;
```

### Logical AND

```js
let found = "";
let result = found && someUndeclaredVariable;
console.log(result); // "" 
```

1. The logical AND (&&) operator is a short-circuited operation, meaning that if the first operand determines the result (its boolean equibalent is false), return the first operant the second operand is never evaluated. Else, it will return the second operand.

### Logical OR

```js
let found = "";
let result = found || someUndeclaredVariable;
console.log(result); //  undefined
```

1. Opposed to logical AND, if the boolean equivalent of the first operand is true, return it. Else, return the second operand.

2. You can also use this behavior to avoid assigning a null or undefined value to a variable.

    ```js
    let myObject = preferredObject || backupObject;
    ```

---

## Multiplicative Operators

1. There are three multiplicative operators in ECMAScript: multiply(*), divide(/), and modulus(%).

2. If either of the operands for a multiplication operation isn’t a number, it is converted to a number behind the scenes using the `Number()` casting function. 

### Multiply

1. The multiply operato has the following unique behaviors when dealing with special value
   1. If the operands are numbers, regular arithmetic multiplication is performed. If the result cannot be represented by ECMAScript, either `Infinity` or `–Infinity` is returned.
   2. If either operand is `NaN`, the result is `NaN`. 
   3. If `Infinity` is multiplied by `0`, the result is `NaN`.
   4. If `Infinity` is multiplied by any finite number other than 0, the result is either `Infinity` or `–Infinity`, depending on the sign of the second operand.
   5. If `Infinity` is multiplied by `Infinity`, the result is `Infinity`. 
   6. If either operand isn’t a number, it is converted to a number behind the scenes using `Number()` and then the other rules are applied.

Multiplier1\Multiplier2| other number | `0` | `Infinity` / `-Infinity` | `NaN`
:- | :- | :- | :- | :-
**other number** | other number / `Infinity` / `-Infinity` | `0` | `Infinity` / `-Infinity` | `NaN`
**`0`** | `0` | `0` | `NaN` | `NaN`
**`Infinity` / `-Infinity`** | `Infinity` / `-Infinity` | `NaN` | `Infinity` / `-Infinity` | `NaN`
**`NaN`** | `NaN` | `NaN` | `NaN` | `NaN`

### Divide 

1. `1 / 0 === Infinity`

2. The divide operator, like the multiply operator, has special behaviors for special values. They are as follows:
    1. If the operands are numbers, regular arithmetic division is performed. If the result can’t be represented in ECMAScript, it returns either `Infinity` or `–Infinity`, e.g. `1 / 0 === Infinity`
    2. If either operand is `NaN`, the result is `NaN`.
    3. If `Infinity` is divided by `Infinity`, the result is `NaN`.
    4. If `zero` is divided by `zero`, the result is `NaN`.
    5. If a nonzero finite number is divided by zero, the result is either `Infinity` or `–Infinity`, depending on the sign of the first operand.
    6. If `Infinity` is divided by any number, the result is either `Infinity` or `–Infinity`, depending on the sign of the second operand.
    7. If either operand isn’t a number, it is converted to a number behind the scenes using `Number()` and then the other rules are applied.

Dividend\Divisor | other number | `0` | `Infinity` / `-Infinity` | `NaN`
:- | :- | :- | :- | :-
**other number** | other number / `Infinity` / `-Infinity` | `Infinity` / `-Infinity` | `0` | `NaN`
**`0`** | `0` | `NaN` | `0` | `NaN`
**`Infinity` / `-Infinity`** | `Infinity` / `-Infinity` | `Infinity` / `-Infinity` | `NaN` | `NaN`
**`NaN`** | `NaN` | `NaN` | `NaN` | `NaN`


### Modulus 

1. Just like the other multiplicative operators, the modulus operator behaves differently for special values, as follows:
   1. If the operands are numbers, regular arithmetic division is performed, and the remainder of that division is returned.
   2. If either operand is `NaN`, the result is `NaN`.
   3. If the dividend is an infinite number, the result is `NaN`.
   4. If the divisor is `0`, the result is `NaN`.
   5. If the dividend is a finite number and the divisor is an infinite number, then the result **is the dividend**.
   6. If the dividend is zero and the divisor is nonzero, the result is zero
   7. If either operand isn’t a number, it is converted to a number behind the scenes using `Number()` and then the other rules are applied.

Dividend\Divisor | other number | `0` | `Infinity` / `-Infinity` | `NaN`
:- | :- | :- | :- | :-
**other number** | other number | `NaN`| other number | `NaN`
**`0`** | `0` | `NaN` | `0` | `NaN`
**`Infinity` / `-Infinity`** | `NaN` | `NaN` | `NaN` | `NaN`
**`NaN`** | `NaN` | `NaN` | `NaN` | `NaN`

---

## Exponentiation Operator

```js
console.log(Math.pow(3, 2); // 9
console.log(3 ** 2); // 9

console.log(Math.pow(16, 0.5); // 4
console.log(16** 0.5); // 4

let squared = 3; 
squared **= 2;
console.log(squared); // 9
```

---

## Additive Operators

As with the multiplicative operators, conversions occur behind the scenes for different data types.

### Add


1. The addition operator either performs string concatenation or numeric addition.

2. Numeric addition: If the **two operands are numbers**, they perform an arithmetic add and return the result according to the following rules:
    1. If either operand is `NaN`, the result is `NaN`.
    2. If `Infinity` is added to `Infinity`, the result is `Infinity`. 
    3. If `–Infinity` is added to `–Infinity`, the result is `–Infinity`. 
    4. If `Infinity` is added to `–Infinity`, the result is `NaN`. 
    5. If `+0` is added to `+0`, the result is `+0`. 
    6. If `–0` is added to `+0`, the result is `+0`.
    7. If `–0` is added to `–0`, the result is `–0`. 

3. The operation return the result according to the following rules:
   1. If **one of the operands is a symbol**, convert it to number using `Number()`, and throw error:
   2. If **one of the operands is a string**, then the following rules apply:
      1. If both operands are strings, the second string is concatenated to the first.
      2. If only one operand is a string, the other operand is converted to a string and the result is the concatenation of the two strings.  
   3. For other primitive values, perform the numeric addition.
   4. If **one of the operands is an object**.
      1. If **the object is not and instance of Date** call `valueOf()` first. If it return an object, call `toString()` method subsequently. and Apply the rules below. 
      2. **If object is an instance of Date**, which is a little different from the other operators, the `toString()` method will perform precede `valueOf()`.

### Subtract

1. Numeric subtraction: they perform an arithmetic subtract and return the result according to the following rules:
    1. If the two operands are numbers, perform arithmetic subtract and return the result.
    2. If either operand is `NaN`, the result is `NaN`.
    3. If `Infinity` is subtracted from `Infinity`, the result is `NaN`. 
    4. If `–Infinity` is subtracted from `–Infinity`, the result is `NaN`. 
    5. If `-Infinity` is subtracted from `Infinity`, the result is `Infinity`. 
    6. If `Infinity` is subtracted from `–Infinity`, the result is `-Infinity`. 
    7. If `+0` is subtracted from `+0`, the result is `+0`. 
    8. **If `–0` is subtracted from `+0`, the result is `-0`.**
    9. If `-0` is subtracted from `–0`, the result is `+0`. 
    10. If either operand is a string, a Boolean, `null`, or `undefined`, it is converted to a number (using `Number()` behind the scenes) and the arithmetic is calculated using the previous rules. 
    11. If either operand is an object, its `valueOf()` method is called to retrieve a numeric value to represent it. If the object doesn’t have `valueOf()` defined or return an object, then `toString()` is called and the resulting string is converted into a number.

---

## Relational Operators

1. Relational operators include less-than (<), greater-than (>), less-than-or-equal-to (<=), and greater-than-or-equal-to (>=).

2. There are some conversions and other oddities that happen when using different data types.
   1. If the both the operands are strings, compare the character codes of each corresponding character in the string. (字典顺序, 按ascii码)
   2. If at least one operand is not string, perform the numeric comparison. If the any operator is not number, convert it to number.
      1. Any relational operation with `NaN` is `false`.
   3. If an operand is an object, call `valueOf()` and use its result to perform the comparison according to the previous rules. If `valueOf()` is not available or returns an object, call `toString()` and use that value according to the previous rules.

---

## Equality Operators

1. ECMAScript provides two sets of operators: **equal (`==`) and not equal (`!=`)** to perform conversion before comparison, and **identically equal (`===`) and not identically equal (`!==`)** to perform comparison without conversion.

### Equal and Not Equal

1. Both operators do conversions to determine if two operands are equal (often called type coercion).

2. When performing conversions, the equal and not-equal operators follow these basic rules:
    1. If the operands are both primitive value
        1. If they have the same type
            - String: return true only if both operands have the same characters in the same order.
            - Number: return true only if both operands have the same value. +0 and -0 are treated as the same value. If either operand is NaN, return false.
            - Boolean: return true only if operands are both true or both false.
        2. If they are different types
            1. Values of `null` and `undefined` are equal. And they cannot be converted into any other values for equality checking. (`null == 0` returns `false`)
            2. Primitive value except `null` and `undefined` will converted to number type for comparison.
                - If either operand is `NaN`, the equal operator returns `false` and the `not-equal` operator returns true. Important note: even if both operands are `NaN`, the equal operator returns `false` because, by rule, `NaN` is not equal to `NaN`.
    2. If one operand is object and the other is not, the `valueOf()` and `toString` methods are called on the object in order to retrieve a **primitive** value to compare according to the previous rules.
    3. If both operands are objects, then they are compared to see if they are the same object. If both operands point to the same object, then the equal operator returns true. Otherwise, the two are not equal.

```js
let result1 = (null < 0); // false, null is converted to 0; 
let result2 = (null == 0); // false, null will not converted to 0.
let result3 = (null <= 0); // true, null is converted to 0;
```

### Identically Equal and Not Identically Equal

1. The identically equal and not identically equal operators do the same thing as equal and not equal, except that they **do not convert operands** before testing for equality. It returns true only if the operands are equal without conversion.

```js
let result1 = ("55" == 55); // true
let result2 = ("55" === 55); // false
let result3 = (null == undefined); // true
let result4 = (null === undefined); // false
```

---

## Conditional Operator

``` javascript
variable = boolean_expression ? true_value : false_value;
```

---

## Assignment Operators

```js
let num = 10
```

---

## Comma Operator

1. The comma operator allows execution of more than one operation in a single statement.
    ```js
    let num1 = 1, num2 = 2, num3 = 3;
    ```

2. it can also be used to assign values. When used in this way, the comma operator always returns the last item in the
expression
    
    ```js
    let num = (5, 1, 4, 8, 0); //num becomes 0
    ```

---

Reference:

- Professional JavaScript for Web Developers 4th Edition
- [算术移位和逻辑移位详解 - 知乎](https://zhuanlan.zhihu.com/p/97749860)
- [JavaScript 标准参考教程: 运算符 - 阮一峰](https://javascript.ruanyifeng.com/grammar/operator.html#toc9)
