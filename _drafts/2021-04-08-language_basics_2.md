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
    - [Devide](#devide)
    - [Modulus](#modulus)

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

1. The bitwise NOT is represented by a tilde (`~`) and simply returns the one’s **complement** (补码) of the number. It negates the number and subtracts 1.

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
`0` | `0` | `0` | `NaN` | `NaN`
`Infinity` / `-Infinity` | `Infinity` / `-Infinity` | `NaN` | `Infinity` / `-Infinity` | `NaN`
`NaN` | `NaN` | `NaN` | `NaN` | `NaN`

### Devide 

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
`0` | `0` | `NaN` | `0` | `NaN`
`Infinity` / `-Infinity` | `Infinity` / `-Infinity` | `Infinity` / `-Infinity` | `NaN` | `NaN`
`NaN` | `NaN` | `NaN` | `NaN` | `NaN`


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
`0` | `0` | `NaN` | `0` | `NaN`
`Infinity` / `-Infinity` | `NaN` | `NaN` | `NaN` | `NaN`
`NaN` | `NaN` | `NaN` | `NaN` | `NaN`


---

Reference:

- Professional JavaScript for Web Developers 4th Edition
- [算术移位和逻辑移位详解 - 知乎](https://zhuanlan.zhihu.com/p/97749860)
